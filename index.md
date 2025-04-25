---
layout: post
title: The Pragmatic Guide to State Machines
---

# The Pragmatic Guide to State Machines

## What is a State Machine?

State machines are one of those concepts that sound more complicated than they really are. Many developers shy away from using them, partly because they're often introduced through heavy academic language—terms like "deterministic automata," "powerset construction," or "mathematical model of computation." That's enough to scare off anyone who's just trying to build software that works.

The good news? You don't need a PhD in computer science to understand or use state machines. In fact, state machines (or *statecharts*, as they're often called in modern tools) are a simple and powerful way to model behavior in many kinds of systems. They're especially useful for reactive systems—systems that respond to external events like button presses, sensor triggers, or user input. Think of elevators, washing machines, or even interactive UIs.

---

## A Simple Explanation

At their core, state machines are made up of **states** and **transitions**.

- A **state** is just a named situation the system can be in—like `Idle`, `Running`, or `Error`.
- A **transition** defines when and how the system moves from one state to another, usually triggered by some kind of input or condition.

There is always one **initial state** where the system starts, and from there, it can move between states based on what's happening in the world around it.

Let's take a very basic example: a light switch.

### Example: A Simple Light Switch

 <iframe src="https://play.itemis.io?model=7ec86474-66d1-4cca-bb60-6f7d91e9601d" allowfullscreen></iframe>

We can model the switch with two states:
- `On`
- `Off`

And one input:
- `buttonPressed`

If the light is `Off` and the button is pressed, we transition to `On`. Press it again, we go back to `Off`.

This might look like overkill for a simple light, but the moment you want to add brightness levels, timers, motion sensors, or modes like "night mode," you'll quickly realize the value of modeling behavior cleanly.

---

## Why Use State Machines?

State machines help you make complex behavior easy to follow and reason about. Instead of writing a huge pile of `if`/`else` or `switch` statements, you describe how your system *behaves*—what states it can be in, how it responds to inputs, and what should happen next.

Some real-world uses include:
- Embedded systems (e.g. coffee machines, vehicle control units)
- UI logic (e.g. wizards, toggle menus, error handling)
- Communication protocols
- Game development (e.g. AI state control)
- Workflow engines

State machines also make it easier to simulate, test, and even generate code for your system—especially when using tools like [itemis CREATE](https://www.itemis.com/en/yakindu/state-machine/), which let you model, simulate, and export code for your charts.

---

## State Machines Aren't Scary

Once you start using state machines, you'll realize they're not some abstract, academic concept—they're a practical and readable way to design systems that respond to real-world events.

In the next chapters, we'll explore how to model richer behavior using powerful concepts like hierarchy, modes, conditions, and events—without getting lost in theory.

Let's keep it simple, visual, and focused on solving real problems.

# Chapter 2: States, Transitions, and Events

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

# Chapter 3: Variables – Giving Your Statechart a Memory

Statecharts become truly powerful when they can *remember* things — and that's exactly what variables allow them to do.

With variables, your statechart can:
- Count how many times something happened
- Track status or mode
- Store values like user input, sensor readings, or error codes
- Make decisions based on internal data

In short, variables give your statechart context — they turn simple reactions into intelligent behavior.

---

## What is a Variable?

A **variable** is a named container for a value. It has a type (like number, text, or boolean) and it can be used in:
- Guard conditions (e.g. `[counter > 3]`)
- Actions (e.g. `counter += 1`)
- Event payloads
- State entry or exit logic

Just like in regular programming, variables in statecharts are used to **store**, **modify**, and **read** data at runtime.

---

## Types of Variables

Most statechart languages support standard data types, such as:

| Type    | Example       | Description                          |
|---------|---------------|--------------------------------------|
| `boolean` | `true`, `false` | Useful for flags and conditions |
| `integer` | `1`, `42`        | Whole numbers                     |
| `real`    | `3.14`, `2.0`    | Decimal numbers                   |
| `string`  | `"hello"`        | Text                              |

In a strongly typed environment, each variable has a fixed type, and type safety is enforced at design time. This means you can't assign a `string` to an `integer`, for example.

---

## Read-Only and Constants

Some variables are marked as `readonly` — they can be changed *within* the statechart, but not from the outside.

Constants (`const`) are variables that never change after they are set. They're great for fixed values like configuration settings or thresholds.

```text
// example
readonly var maxRetries : integer = 3
const appName : string = "StateMaster"
```

---

## Expressions and Assignments

Variables come into play when you're writing **guards** and **actions**. Here are a few common things you can do with them:

### In Guards

```text
// Only transition if counter is less than 5
buttonPressed [counter < 5] / counter += 1
```

### In Actions

```text
// When triggered, reset the error flag
reset / errorActive = false
```

You can combine variables with arithmetic, logical operations, and conditional expressions to model more complex logic.

---

## Example: A Simple Switch Counter

Let's say we have a light that toggles between `On` and `Off`, and we want to count how many times the light was turned on.

```text
// Variable declaration
var switchCount : integer = 0
```

### States and Transitions

```text
// State: Off
entry / log("Entering Off")
exit / log("Leaving Off")

// Transition: Off -> On
buttonPressed / switchCount += 1
```

```text
// State: On
entry / log("Light turned on " + switchCount + " times")
exit / log("Leaving On")

// Transition: On -> Off
buttonPressed
```

Each time the light is turned on, the counter increases. When entering the `On` state, it logs how many times the switch was used. The `Off` state also logs entry and exit to show variable usage there too.

---

## Type Safety

In a statically typed system, you'll get feedback immediately if you try something invalid — like assigning a boolean to a number. This prevents bugs and makes your statechart more robust.

You can also cast between types when needed:

```text
// Cast from real to integer (might truncate)
x = y as integer
```

---

## Summary

Variables are the memory of your statechart. Without them, you can only model behavior in terms of "what just happened." With variables, you can also model **what has happened before**, and **what is currently true**.

They help you:
- Build smarter, more responsive behavior
- Store internal state cleanly
- Avoid cluttering your chart with unnecessary states

As a rule of thumb:

> If you find yourself duplicating similar states just to track a number or flag — it might be time to use a variable instead.

In the next chapter, we'll explore how statecharts grow in complexity, and how hierarchy and parallelism help manage that complexity in a clean and scalable way.

# Chapter 4: Composite States – Organizing Behavior with Hierarchy

As your statechart grows, you'll likely end up with more and more states. Things can get messy fast — diagrams become hard to read, logic gets harder to follow, and reuse becomes difficult.

This is where **composite states** come in. They let you group related states together into one logical unit and build **hierarchical** statecharts that scale much better as complexity increases.

---

## What is a Composite State?

A **composite state** is a state that contains a nested region with its own substates and transitions. You can think of it like a mini-statechart embedded inside another state.

When the composite state is active, it delegates control to its internal region. This helps organize logic, isolate complexity, and reuse patterns across different parts of your system.

---

## Why Use Composite States?

Composite states are useful when:
- A state represents a multi-step process
- You want to group related states with common behavior
- You need to split up a large, flat statechart into manageable pieces
- You want to create reusable sub-behaviors or modes

---

## How Composite States Work

When a composite state is entered, it behaves just like a new statechart:
- It starts at a special **entry point**
- A substate becomes active
- Execution continues within the nested structure

When the composite state is exited:
- All active substates are exited
- The composite state itself is exited

---

## Entry Points

Every composite state must define **how it should be entered**.

There are two kinds of entry points:
- **Default entry point**: the standard starting point (like UML's initial state)
- **Named entry points**: custom entry paths that allow more flexible control flow

Named entry points are especially useful when the same composite state should behave differently depending on how it is entered.

---

## Exit Points

Just like entries define how a composite state begins, **exit points** define how it ends. An exit point is a special marker that signals the end of execution for that composite state — and allows the outer statechart to respond accordingly.

There are two types of exit points:
- **Default exit point**: used for general exits
- **Named exit points**: allow different transitions to be triggered depending on how the composite state finishes

This allows for powerful and clear branching logic. For example, a composite state handling a process might exit with a different label depending on whether the result was a success or a failure. The outer statechart can then continue accordingly.

---

## Example: Process and Result Handling

Imagine a composite state called `Process`, which contains two steps: `A` and `B`. If both succeed, the process should transition to a handler for successful outcomes. If an error occurs, it should instead transition to a failure handler.

Now imagine another composite state called `HandleResult`, which contains different behaviors depending on whether success or failure was encountered.

Entry and exit points allow this exact kind of pattern:
- `Process` exits via a `success` or `error` point
- `HandleResult` begins at the appropriate matching entry

This makes the logic expressive, modular, and easy to follow — and it avoids cluttering the outer statechart with unnecessary intermediate states.

---

## Summary

Composite states give your statecharts **structure**, **clarity**, and **scalability**. They help you:

- Avoid flat, hard-to-read diagrams
- Encapsulate behavior cleanly
- Control how behavior begins and ends using **entry** and **exit** points
- Model modes and processes in a clear and reusable way

In the next chapter, we'll explore **orthogonal states** — another powerful way to manage complexity by running states *in parallel*.

# Chapter 5: Orthogonal States – Modeling Concurrency Cleanly

In real-world systems, things often happen at the same time — or at least, independently. For example, a washing machine might be heating water while spinning the drum. A robot might be tracking its position while checking for obstacles.

To model this kind of behavior, statecharts support **orthogonal states** — a way to express **concurrent behavior** within a single statechart.

---

## What Are Orthogonal States?

An **orthogonal state** is essentially a composite state that contains **multiple regions**. Each region can have its own set of substates, transitions, and logic. These regions are executed **virtually in parallel**.

> Important: **Orthogonal states are not true parallel execution.**  
> They are evaluated **sequentially**, one after another, during each execution cycle.

This design is intentional. Orthogonality is about modeling *logical concurrency*, not multithreading. You don't have to deal with synchronization, race conditions, or thread safety — you just model separate, independent behaviors that **run side-by-side** from a conceptual point of view.

---

## Why Use Orthogonal States?

Orthogonal states are ideal when:
- You want to model **independent modes or components** within a system
- You have multiple **subsystems that operate in parallel**
- You want to avoid tangled logic by splitting responsibilities across regions

Examples:
- In a smart home system, one region controls the lights, another handles heating.
- In a vehicle, one region manages cruise control, another handles lane keeping.

Each part has its own logic, but they run **together** as long as the parent orthogonal state is active.

---

## How Execution Works

When an orthogonal state is entered:
- All of its regions are entered simultaneously (logically speaking)
- Each region activates its own initial substate

During each execution cycle:
- All regions are evaluated **in a defined order** (typically top-to-bottom, left-to-right)
- Transitions in one region do **not block** or delay transitions in other regions

This allows multiple independent state machines to share a synchronized clock, yet evolve independently.

When an orthogonal state is exited:
- All active substates in **all regions** are exited

---

## Synchronization and Coordination

Sometimes you'll want to coordinate between regions. This can be done using:
- **Events** raised by one region and handled in another
- **Variables** shared across regions
- **Exit and entry points** combined with synchronization nodes (in some tools)

This is how orthogonal regions can still collaborate — even though they're logically separate.

---

## Summary

Orthogonal states are a powerful tool for modeling **concurrent, but independent** behaviors within a single system.

They help you:
- Keep related concerns separated
- Model multiple control flows cleanly
- Avoid complex nesting by distributing logic across parallel regions

Just remember: orthogonality is **not true parallelism**. It's a way to think about things happening side by side — while still executing in a **defined, sequential order**.

In the next chapters, we'll look at more advanced features like **history states**, **choice nodes**, and **final states**, which add even more power to your modeling toolbox.

# Chapter 6: Final States, Choices, and History – Controlling Flow and Remembering State

As you model more complex behavior in a statechart, you'll need additional tools to control how execution flows and how the system "remembers" what it was doing. In this chapter, we introduce three important concepts that extend the power of your statecharts:

- **Final states** to indicate completion
- **Choice nodes** to make conditional decisions
- **History states** to remember previously active states

---

## Final States – Marking the End of a Flow

A **final state** marks the end of a state machine or region. When the system reaches a final state, that region stops executing and becomes inactive.

Final states are typically used to:
- Indicate completion of a task or process
- Trigger transitions to a new phase
- Coordinate across parallel regions

Each region can have **one final state**, and it may have **multiple incoming transitions**.

> If a state machine has multiple orthogonal regions, execution only proceeds when **all** regions have reached their final state.

Final states are different from **exit points**, which are used to leave a composite state. A final state means "we're done here"; an exit point means "we're leaving early."

---

## Choice Nodes – Making Conditional Decisions

A **choice node** is a pseudo-state that lets you branch conditionally after a transition.

Here's how it works:
- A transition leads into a choice node
- Multiple transitions lead out, each with a guard condition
- The first transition whose guard evaluates to `true` is taken
- If no guard matches, a **default transition** should be provided (using a trigger like `else` or `default`)

This pattern is useful when the system needs to evaluate data and choose between multiple paths.

### Example:

Imagine a quiz system that branches depending on the score:
- If `score >= 80`, go to `PassWithDistinction`
- If `score >= 50`, go to `Pass`
- Otherwise, go to `Fail`

A choice node makes this logic explicit and easy to model.

---

## History States – Picking Up Where You Left Off

A **history state** allows a composite state to remember which substate was active the last time it was exited. The next time the composite state is entered, it resumes at that remembered state instead of starting from the beginning.

There are two types of history states:
- **Shallow history**: remembers the active state **one level deep**
- **Deep history**: remembers the active state **recursively**, including any nested composite states

History states are useful when:
- You want to **pause and resume** processes
- You want to **save user progress**
- You want to avoid repeating already completed steps

### Shallow History Example

Imagine a form with multiple steps inside a composite state called `answeringQuestions`. At any time, the user can press "Pause", which returns the system to an outer state like `Init`. Later, when they hit "Continue", the system resumes **exactly where they left off** — thanks to the shallow history state.

Shallow history tracks only the most recent active substate in that region.

### Deep History Example

If your substates themselves contain **nested composite states**, and you want to resume the entire nested hierarchy, use a **deep history state**. It remembers not just one level, but the full internal configuration.

This is especially useful in systems with complex workflows, multiple levels of modes, or nested user interfaces.

---

## Summary

These advanced flow control tools give you more flexibility and precision in your statecharts:

- **Final states** signal completion
- **Choice nodes** enable dynamic branching
- **History states** preserve execution context across interruptions

Used correctly, they can make your models more robust, interactive, and user-friendly — especially in systems where users pause, resume, retry, or take different paths based on conditions.

In the next chapter, we'll explore how all these concepts come together in real-world patterns and best practices for designing readable, scalable statecharts.

# Chapter 7: Summary and Next Steps

Congratulations! You've now worked through the foundational concepts of modern statecharts — from the basics to more advanced modeling techniques. Whether you're building embedded systems, user interfaces, automation logic, or reactive workflows, statecharts offer a powerful, visual, and maintainable way to model behavior.

---

## What You've Learned

Here's a quick recap of what we've covered:

- **Chapter 1: What is a State Machine?**  
  You learned the core idea of modeling behavior as a set of states and transitions triggered by events.

- **Chapter 2: States, Transitions, and Events**  
  You explored how states define behavior, transitions connect them, and events trigger movement through the system.

- **Chapter 3: Variables – Giving Your Statechart a Memory**  
  You added memory and logic to your models through variables, expressions, and guard conditions.

- **Chapter 4: Composite States – Organizing Behavior with Hierarchy**  
  You discovered how to simplify large diagrams using nested statecharts with entry and exit points.

- **Chapter 5: Orthogonal States – Modeling Concurrency Cleanly**  
  You learned how to model independent behaviors that run side-by-side using orthogonal regions.

- **Chapter 6: Final States, Choices, and History**  
  You mastered tools to control flow and preserve state, including final states, conditional branching, and both shallow and deep history.

---

## Why This Matters

Statecharts aren't just an academic tool — they're practical, scalable, and designed to make complex behavior **easier to understand** and **easier to build**.

Used correctly, they help you:
- Make requirements visual and precise
- Reduce bugs in complex workflows
- Improve maintainability and reuse
- Align teams on system behavior

---

## What's Next?

Now that you've learned the theory, it's time to get your hands dirty. Try modeling a real system — something from your work, a UI flow, or a home automation process. Start simple, then grow complexity with hierarchy, concurrency, and history when needed.

You might also explore:
- Simulation and testing of statecharts
- Code generation for embedded systems or web applications
- Modeling patterns and best practices
- Integrating statecharts into larger software architectures

---

## Try itemis CREATE

To apply your knowledge and explore further, consider using [itemis CREATE](https://create.itemis.io), a powerful tool for modeling, simulating, and generating code from statecharts.

### Key Features:
- **Online Editor**: Model and simulate directly in your browser with the [itemis CREATE Cloud Editor](https://info.itemis.com/en/products/itemis-create/online-web-editor).
- **Examples Repository**: Access a variety of [examples](https://www.itemis.com/en/products/itemis-create/documentation/examples) to see statecharts in action.
- **Code Generation**: Generate high-quality source code in languages like C, C++, Java, Python, and more.
- **Comprehensive Documentation**: Learn more through the [itemis CREATE Documentation](https://www.itemis.com/en/products/itemis-create/documentation/user-guide).

---

## Final Thoughts

The power of statecharts lies in their clarity. They help you think better about behavior — and that alone makes them worth learning.

> Keep your models clean, your states meaningful, and your transitions intentional.

And most of all: enjoy the journey.

Happy modeling!
