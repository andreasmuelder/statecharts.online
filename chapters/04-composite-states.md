---
title: Composite States – Organizing Behavior with Hierarchy
layout: chapter
---

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

## Example: A Lightswitch With Two Different Modes

This statechart models a lighting system with two main modes: `Manual` and `MotionSensing`. In `Manual` mode, the user can toggle the light on or off, while in `MotionSensing` mode, the light responds automatically to detected motion and turns off after a set period. The system allows switching between modes and manages brightness accordingly.

In the provided statechart model, composite states are used to encapsulate the behavior of the lighting system in two distinct modes: Manual and MotionSensing. Each of these modes has its own internal states and transitions, which are managed independently within their respective composite states. 

Composite states allow the statechart to be broken down into smaller, more manageable sections. By encapsulating the behavior of the lighting system in separate modes, the statechart becomes easier to read and understand. Each composite state can be viewed as a self-contained module that handles specific functionality, such as manual control or motion sensing.

 <iframe src="https://play.itemis.io?model=8abee2f2-e76d-456d-83d5-87eab96b0475" width="100%" height="800px" style="border: 1px solid" allowfullscreen></iframe>

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

Ready to continue? Head over to [Chapter 5: Orthogonal States – Modeling Concurrency Cleanly](05-orthogonal-states.md) 