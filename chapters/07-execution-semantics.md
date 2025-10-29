---
title: Execution Semantics – How Statecharts Run and Process Events
date: 2025-10-06
last_modified_at: 2025-10-06
layout: chapter
description: Understand the execution model of statecharts, event processing order, and the run-to-completion semantics that govern behavior.
faqs:
  - question: What are execution semantics in statecharts?
    answer: >
      Execution semantics define the rules for how a statechart processes events,
      executes transitions, and manages state changes in a predictable way.
  - question: What is run-to-completion?
    answer: >
      Run-to-completion means that when an event is processed, all resulting
      transitions and actions complete before the next event can be processed.
  - question: What's the difference between event-driven and cycle-based execution?
    answer: >
      Event-driven execution waits for external events to trigger state changes,
      while cycle-based execution continuously polls and processes events in a loop.
  - question: What are single steps vs super steps?
    answer: >
      Single steps execute one transition at a time, while super steps execute
      multiple transitions until a stable state is reached in one atomic operation.
---

Understanding how statecharts execute is crucial for building reliable, predictable systems. The **execution semantics** define the fundamental rules that govern how events are processed, transitions are taken, and actions are executed. These rules ensure that statechart behavior is deterministic and reliable, regardless of the complexity of your model.

This chapter explores the core execution concepts that make statecharts powerful and predictable in real-world applications.

We’ll move fast and in the most compact order: first the atomic guarantee (run‑to‑completion), then step granularity (single vs super), then how engines schedule steps (event‑driven vs cycle‑based), followed by queuing rules.

---
 

## Run-To-Completion Semantics

The **Run-To-Completion (RTC)** principle is fundamental to statechart execution and ensures predictable behavior:

### What is Run-To-Completion?

A Run-To-Completion step is an **atomic execution unit** that:
- Transforms the statechart from one valid state configuration to the next
- Processes one event completely before handling the next
- Prevents event interleaving and race conditions
- Guarantees the system reaches a stable state after each step

### The RTC Process

Conceptually, an RTC step is a bounded atomic transition sequence. The engine takes the next event from the queue and evaluates the model to see which transitions are enabled. It then leaves the current configuration by running exit actions from the innermost active states outward, performs the transition's own actions, and enters the target configuration from the outside in. If actions raised internal events, those are handled as part of the same step until the machine reaches a stable situation where no further transitions are enabled. Only then does the engine consider the step complete and move on to the next event.

### RTC Step Example

Let's examine how RTC works in practice with a concrete example. When you raise the event `doIt`, the run-to-completion step executes the following sequence:

1. **Exit action** of `InnerState` is executed (`x += 1`)
2. **Exit action** of `State1` is executed (`x += 1`) 
3. **Transition action** of the `doIt` transition is executed (`x++`)
4. **Entry action** of `State2` is executed (raises `internalEvent`)
5. The `internalEvent` is added to the internal event queue and processed in the same RTC step
6. The transition triggered by `internalEvent` executes, moving to the final state

All this happens within one **atomic step**. The final state is reached and the value of `x` is `3`.

 <iframe src="https://play.itemis.io?model=540e95ea-7e38-4c00-b1b7-d825955b8413" width="100%" height="500px" style="border: 1px solid" allowfullscreen></iframe>


The Run-To-Completion model provides several crucial advantages for reliable system behavior. It ensures deterministic behavior where the same input sequence always produces the same output, eliminates race conditions by preventing events from interrupting each other, provides predictable timing since each event is fully processed before the next one begins, and makes debugging significantly easier through clear cause-and-effect relationships that can be traced step by step.

---

## Single Steps vs. Super Steps

There are two common ways to pace execution through a chain of transitions. With **single‑step semantics**, one RTC step advances the machine by at most one state change along a path. A transient state with an unconditional outgoing transition will still be observed for one step; the next step continues the journey. This makes timing and tracing very predictable because every visible change corresponds to a distinct step.

With **super‑step semantics**, the engine continues to take transitions within a single RTC step until the configuration becomes stable—no further transitions are enabled. That lets you “skip through” transient states in one go and can speed up models that use many such states. The trade‑off is that the amount of work per step becomes data‑dependent, and care must be taken to avoid accidental infinite loops; most engines enforce limits to keep execution bounded. In practice, single‑step semantics are a great default for testability and real‑time constraints, while super‑steps can be helpful for conceptual simplicity when traversing transient states quickly is desired.

In this statechart with **super-steps disabled**, when you raise the `clicked` event, the machine transitions from `A` to `B` and stops. The variable `x` is set to `21` by the entry action of state `B`. Even though there's an immediate transition from `B` to `C`, it won't be taken until the next `clicked` event is raised.

 <iframe src="https://play.itemis.io?model=78382a20-2f3f-4757-a979-461c39336d2a" width="100%" height="400px" style="border: 1px solid" allowfullscreen></iframe>

In this statechart with **super-steps enabled**, when you raise the `clicked` event, the machine transitions from `A` through `B` all the way to `C` in **one atomic step**. Since `C` has no outgoing transitions, the local reaction is also executed, setting `x` to `42`.

 <iframe src="https://play.itemis.io?model=8e90d984-4ced-4234-9288-89cc336b8966" width="100%" height="400px" style="border: 1px solid" allowfullscreen></iframe>

### When to Use Each

**Use single-step semantics when:**
- You need predictable, bounded execution time (real-time systems)
- You want to observe or test intermediate states
- Timing and traceability are critical

**Use super-step semantics when:**
- Transient states are just implementation details
- You want faster progress through state chains
- Conceptual simplicity outweighs traceability needs

> **Best Practice**: Most engines default to single-step for safety. Use super-steps deliberately and test thoroughly to avoid infinite loops.

### Emulating Super Steps

You can achieve super-step-like behavior in single-step systems using **internal events** (like I did in the example about Run-To-Completion Semantics). When an action raises an internal event, that event is queued and processed in the next RTC step, creating a chain of back-to-back transitions without external involvement. This approach provides the conceptual simplicity of super-steps while maintaining the predictability and traceability of single-step execution.

---

## Event-Driven vs. Cycle-Based Execution

With RTC and step granularity established, it’s easier to understand the two common ways engines trigger those atomic steps. Statecharts can operate in two fundamental execution modes, each suited to different types of systems:

### Event-Driven Execution

In **event-driven mode**, the statechart remains idle until an external event occurs. When an event is received, the event triggers the execution engine which evaluates all enabled transitions, executes the appropriate transitions, and then returns the system to an idle state. This creates a reactive pattern where the statechart only consumes resources when something actually happens.

This mode works particularly well for reactive systems that respond to user input, embedded systems with interrupt-driven architectures, UI applications that react to button clicks and user actions, and any systems with sporadic events where continuous polling would waste precious computational resources.

### Cycle-Based Execution

In **cycle-based mode**, the statechart continuously executes in a loop, regularly checking for new events in the event queue, processing any events and executing transitions, updating the current state configuration, and then repeating the entire cycle. This creates a predictable execution pattern with regular intervals.

This mode works particularly well for control systems that need regular monitoring of sensors and actuators, real-time systems with predictable timing requirements, simulation environments where time advances in discrete steps, and systems with time-based behavior that need regular evaluation of conditions and timeouts.

> **Key Insight**: The choice between event-driven and cycle-based execution affects how you design your statechart, particularly regarding timing, resource usage, and responsiveness.

---


## Event Queuing and Processing

### Internal vs. External Events

**External Events** are raised by the environment or other system components outside the statechart itself. These events are queued in the main event queue and processed according to FIFO ordering, ensuring that events are handled in the sequence they arrived.

**Internal Events** are raised within the statechart during execution, often as a result of actions or transitions. These events are typically processed immediately or placed in a separate internal queue, and they enable communication between orthogonal regions within the same statechart.

### Best Practices for Event Handling

When designing event handling in your statecharts, several important practices will help ensure reliable behavior. Avoid recursive event raising by never raising events from operation callbacks during event processing, as this can lead to unpredictable execution patterns. Instead, use internal events for chaining transitions and state-to-state communication within your statechart. Always ensure that external events are queued properly so they're processed in the correct order, and plan for event overflow situations where events might arrive faster than your system can process them.

---

## Summary

Execution semantics make statecharts dependable. Event‑driven machines react only when something happens; cycle‑based machines advance at a steady cadence. Run‑to‑completion guarantees atomic, deterministic handling of each event. Single‑step execution favors traceability and timing predictability, while super‑steps accelerate progress through transient states and therefore need safeguards. With clear queuing rules for internal and external events, your models remain efficient, testable, and easy to reason about.

---

{%- include faq.html -%}

---

<div style="display: flex; justify-content: space-between; width: 100%; align-items: center;">
  <div style="text-align: left; flex: 2;"><a href="06-final-states-choices-and-history.html">← Previous: Final States, Choices, and History</a></div>
  <div style="text-align: center; flex: 1;"><a href="#top">Back to top</a></div>
  <div style="text-align: right; flex: 2;"><a href="08-implementation-strategies.html">Next: Implementation Strategies →</a></div>
</div>