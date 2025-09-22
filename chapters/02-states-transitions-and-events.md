---
title: States, Transitions, and Events
layout: chapter
description: Understand the core building blocks of statecharts — states, transitions, and events — and how they define behavior and flow.
---

At the heart of every statechart are **states**, **transitions**, and **events**. These core elements describe how your system behaves, how it reacts, and how it evolves over time in response to inputs. Let's break them down step by step.

## States

A **state** represents a specific situation or mode your system can be in. Think of states like "Idle," "Processing," or "Error." At any point during execution, your system is in one or more active states, depending on whether you're modeling simple logic or more complex concurrent behavior.

States are not just passive flags — they can do work. You can define actions that run when:
- A state is **entered**
- A state is **active**
- A state is **exited**

This allows your system to react immediately when something changes. For example, a state can initialize a variable when entered, perform calculations while active, and clean up when exited.

## Transitions

A **transition** connects two states. It defines when and why the system should move from one state to another.

Each transition listens for certain **events**, and optionally checks conditions (called **guards**) before taking action. If the event happens and the guard is true, the transition is taken—and any associated actions are executed.

A typical transition answers three questions:
- **What triggered it?** (e.g. a button press or a timer)
- **Should it run?** (e.g. is a condition met?)
- **What should it do?** (e.g. change a value or send a signal)

## Events

**Events** are how the world talks to your statechart—and how your statechart talks back.

There are two kinds:
- **Incoming events** are raised by the outside world to tell the statechart something happened (e.g. a button was clicked).
- **Outgoing events** are raised by the statechart itself to signal something to the outside (e.g. an operation finished or an error occurred).

In some tools you can also use **timed events** (like "after 3 seconds") and **pseudo-events** (like "always" or "on every cycle") to express more complex behavior.

Events can optionally carry a **payload**—a value that adds more context. For example, a `buttonClicked` event might carry the ID of the button that was pressed.

---

## Execution Flow

When a transition is taken, things happen in a well-defined order:

1. The **exit actions** of the current state are executed.
2. The **transition actions** are executed.
3. The **entry actions** of the new state are executed.

This order matters. It ensures that the system cleans up its old state before reacting to the new one.

### Example


 <iframe src="https://play.itemis.io?model=b7eae1ae-c866-4b1a-91cc-d07d925236b3" width="100%" height="400px" style="border: 1px solid" allowfullscreen></iframe>

This statechart effectively models a simple process flow where the system starts in an `Idle` state, processes an event upon receiving `startProcessing`, and then returns to `Idle` after a 2-second delay, signaling completion with the `processingDone` out event. This example highlights the use of states, transitions, and events to manage system behavior.

---

## Summary

States, transitions, and events are the foundation of any statechart. They allow you to build behavior that is:
- Easy to understand
- Easy to test
- Easy to extend

As you model more complex systems, these simple concepts remain the same. You'll just add more structure—like nested states or parallel regions—to manage complexity.

But at its core, it's still all about answering:
- Where are we now? (**State**)
- What led to us being here? (**Event**)
- What options do we have now? (**Transition**)

Ready to continue? Head over to [Chapter 3: Variables – Giving Your Statechart a Memory](03-variables.md) 