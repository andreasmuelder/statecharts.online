---
title: States, Transitions, and Events
layout: chapter
---

At the heart of every statechart are **states**, **transitions**, and **events**. These core elements describe how your system behaves, how it reacts, and how it evolves over time in response to inputs. Let's break them down step by step.

---

## States

A **state** represents a specific situation or mode your system can be in. Think of states like "Idle," "Processing," or "Error." At any point during execution, your system is in one or more active states, depending on whether you're modeling simple logic or more complex concurrent behavior.

States are not just passive flags—they can do work. You can define actions that run when:
- A state is **entered**
- A state is **active**
- A state is **exited**

This allows your system to react immediately when something changes. For example, a state can initialize a variable when entered, perform calculations while active, and clean up when exited.

---

## Transitions

A **transition** connects two states. It defines when and why the system should move from one state to another.

Each transition listens for certain **events**, and optionally checks conditions (called **guards**) before taking action. If the event happens and the guard is true, the transition is taken—and any associated actions are executed.

A typical transition answers three questions:
- **What triggered it?** (e.g. a button press or a timer)
- **Should it run?** (e.g. is a condition met?)
- **What should it do?** (e.g. change a value or send a signal)

### Example

Imagine a simple system with two states: `Off` and `On`. When the user presses a button, an event like `buttonPressed` is raised. If the system is in `Off`, the event triggers a transition to `On`. That transition might, for example, turn on a light.

Transitions can also include logic like checking if a counter has reached a limit or if a sensor reports a certain value. This keeps your system flexible and expressive.

---

## Events

**Events** are how the world talks to your statechart—and how your statechart talks back.

There are two kinds:
- **Incoming events** are raised by the outside world to tell the statechart something happened (e.g. a button was clicked).
- **Outgoing events** are raised by the statechart itself to signal something to the outside (e.g. an operation finished or an error occurred).

You can also use **timed events** (like "after 3 seconds") and **pseudo-events** (like "always" or "on every cycle") to express more complex behavior.

Events can optionally carry a **payload**—a value that adds more context. For example, a `buttonClicked` event might carry the ID of the button that was pressed.

---

## Execution Flow

When a transition is taken, things happen in a well-defined order:

1. The **exit actions** of the current state are executed.
2. The **transition actions** are executed.
3. The **entry actions** of the new state are executed.

This order matters. It ensures that the system cleans up its old state before reacting to the new one.

### Example

Let's say you're in a state called `Idle`, and when the user presses a button (`buttonPressed`), you transition to a state called `Running`. Here's what might happen:
- `Idle` state's exit action resets a timer.
- The transition assigns a value to a variable.
- `Running` state's entry action starts a motor.

This predictable flow keeps the system logic clean and easy to follow.

---

## Summary

States, transitions, and events are the foundation of any statechart. They allow you to build behavior that is:
- Easy to understand
- Easy to test
- Easy to extend

As you model more complex systems, these simple concepts remain the same. You'll just add more structure—like nested states or parallel regions—to manage complexity.

But at its core, it's still all about answering:
- Where are we now? (**State**)
- What just happened? (**Event**)
- What should we do about it? (**Transition**)

Ready to continue? Head over to [Chapter 3: Variables – Giving Your Statechart a Memory](03-variables.md) 