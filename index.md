# The Pragmatic Guide to State Machines

## What is a State Machine?

State machines are one of those concepts that sound more complicated than they really are. Many developers shy away from using them, partly because they’re often introduced through heavy academic language—terms like "deterministic automata," "powerset construction," or "mathematical model of computation." That’s enough to scare off anyone who's just trying to build software that works.

The good news? You don’t need a PhD in computer science to understand or use state machines. In fact, state machines (or *statecharts*, as they’re often called in modern tools) are a simple and powerful way to model behavior in many kinds of systems. They're especially useful for reactive systems—systems that respond to external events like button presses, sensor triggers, or user input. Think of elevators, washing machines, or even interactive UIs.

---

## A Simple Explanation

At their core, state machines are made up of **states** and **transitions**.

- A **state** is just a named situation the system can be in—like `Idle`, `Running`, or `Error`.
- A **transition** defines when and how the system moves from one state to another, usually triggered by some kind of input or condition.

There is always one **initial state** where the system starts, and from there, it can move between states based on what’s happening in the world around it.

Let’s take a very basic example: a light switch.

### Example: A Simple Light Switch

 <iframe src="https://play.itemis.io?model=7ec86474-66d1-4cca-bb60-6f7d91e9601d" allowfullscreen></iframe>

We can model the switch with two states:
- `On`
- `Off`

And one input:
- `buttonPressed`

If the light is `Off` and the button is pressed, we transition to `On`. Press it again, we go back to `Off`.

This might look like overkill for a simple light, but the moment you want to add brightness levels, timers, motion sensors, or modes like “night mode,” you’ll quickly realize the value of modeling behavior cleanly.

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

## State Machines Aren’t Scary

Once you start using state machines, you’ll realize they’re not some abstract, academic concept—they're a practical and readable way to design systems that respond to real-world events.

In the next chapters, we’ll explore how to model richer behavior using powerful concepts like hierarchy, modes, conditions, and events—without getting lost in theory.

Let’s keep it simple, visual, and focused on solving real problems.

# Chapter 2: States, Transitions, and Events

At the heart of every statechart are **states**, **transitions**, and **events**. These core elements describe how your system behaves, how it reacts, and how it evolves over time in response to inputs. Let’s break them down step by step.

---

## States

A **state** represents a specific situation or mode your system can be in. Think of states like “Idle,” “Processing,” or “Error.” At any point during execution, your system is in one or more active states, depending on whether you’re modeling simple logic or more complex concurrent behavior.

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

You can also use **timed events** (like “after 3 seconds”) and **pseudo-events** (like “always” or “on every cycle”) to express more complex behavior.

Events can optionally carry a **payload**—a value that adds more context. For example, a `buttonClicked` event might carry the ID of the button that was pressed.

---

## Execution Flow

When a transition is taken, things happen in a well-defined order:

1. The **exit actions** of the current state are executed.
2. The **transition actions** are executed.
3. The **entry actions** of the new state are executed.

This order matters. It ensures that the system cleans up its old state before reacting to the new one.

### Example

Let’s say you're in a state called `Idle`, and when the user presses a button (`buttonPressed`), you transition to a state called `Running`. Here’s what might happen:
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

As you model more complex systems, these simple concepts remain the same. You’ll just add more structure—like nested states or parallel regions—to manage complexity.

But at its core, it’s still all about answering:
- Where are we now? (**State**)
- What just happened? (**Event**)
- What should we do about it? (**Transition**)

# Chapter 3: Variables – Giving Your Statechart a Memory

Statecharts become truly powerful when they can *remember* things — and that’s exactly what variables allow them to do.

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

In a strongly typed environment, each variable has a fixed type, and type safety is enforced at design time. This means you can’t assign a `string` to an `integer`, for example.

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

Let’s say we have a light that toggles between `On` and `Off`, and we want to count how many times the light was turned on.

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

In the next chapter, we’ll explore how statecharts grow in complexity, and how hierarchy and parallelism help manage that complexity in a clean and scalable way.

