---
title: History, Choices and the Final State – Controlling Flow and Remembering State
date: 2025-09-22
last_modified_at: 2025-09-22
layout: chapter
description: Master final states, choice nodes, and history to control flow and resume behavior where it left off.
faqs:
  - question: What is a final state?
    answer: >
      A terminal state that marks completion of a state machine or region. When
      reached, that region stops executing.
  - question: What is a choice node?
    answer: >
      A pseudo-state that branches conditionally; the first outgoing transition
      whose guard is true is taken, with a default as fallback.
  - question: What is a history state?
    answer: >
      A mechanism to remember the last active substate when leaving a composite
      state and to resume there upon re-entry. Supports shallow and deep modes.
  - question: When should I use shallow vs deep history?
    answer: >
      Use shallow to remember one level of nesting; use deep to restore the full
      nested configuration of substates.
---

As you model more complex behavior in a statechart, you'll need additional tools to control how execution flows and how the system "remembers" what it was doing. In this chapter, we introduce three important concepts that extend the power of your statecharts:

- **Final states** to indicate completion
- **Choice nodes** to make conditional decisions
- **History states** to remember previously active states

## Example: A Survey with a History, Choice and Final State

Imagine a form with multiple steps inside a composite state called `Survey`. At any time, the user can press "Pause", which returns the system to an outer state like `Init`. Later, when they hit "Continue", the system resumes **exactly where they left off** — thanks to the shallow history state.

The `Survey` Statechart also has a `Choice` State, depending on the answer of `Question 2` it will delegate either to `Question 3 a` or `Question 3 b`. Think about a Choice node as an `if/then/else` or a `switch/case`.

Finally, if all questions are answered, the Statemachine will reach its `Final State`.

 <iframe src="https://play.itemis.io?model=087929ff-7093-4493-9421-4c253c13a672" width="100%" height="800px" style="border: 1px solid" allowfullscreen></iframe>

## History States – Picking Up Where You Left Off

A **history state** allows a composite state to remember which substate was active the last time it was exited. The next time the composite state is entered, it resumes at that remembered state instead of starting from the beginning.

There are two types of history states:
- **Shallow history**: remembers the active state **one level deep**
- **Deep history**: remembers the active state **recursively**, including any nested composite states

History states are useful when:
- You want to **pause and resume** processes
- You want to **save user progress**
- You want to avoid repeating already completed steps

### Shallow History

At any time, the user can press `Pause`, which returns the system to the outer state `Pause`. Later, when they hit `Resume`, the system resumes **exactly where they left off** — thanks to the shallow history state.

Shallow history tracks only the most recent active substate in that region.

### Deep History

If your substates themselves contain **nested composite states**, and you want to resume the entire nested hierarchy, use a **deep history state**. It remembers not just one level, but the full internal configuration.

This is especially useful in systems with complex workflows, multiple levels of modes, or nested user interfaces.

---

## Choice Nodes – Making Conditional Decisions

A **choice node** is a pseudo-state that lets you branch conditionally after a transition.

Here's how it works:
- A transition leads into a choice node
- Multiple transitions lead out, each with a guard condition
- The first transition whose guard evaluates to `true` is taken
- If no guard matches, a **default transition** should be provided 

This pattern is useful when the system needs to evaluate data and choose between multiple paths.
A choice node makes this logic explicit and easy to model.

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

## Summary

These advanced flow control tools give you more flexibility and precision in your statecharts:

- **Final states** signal completion
- **Choice nodes** enable dynamic branching
- **History states** preserve execution context across interruptions

Used correctly, they can make your models more robust, interactive, and user-friendly — especially in systems where users pause, resume, retry, or take different paths based on conditions.

In the next chapter, we'll explore how all these concepts come together in real-world patterns and best practices for designing readable, scalable statecharts.

---

{%- include faq.html -%}

---


Ready to continue? Head over to [Chapter 7: Implementation Strategies](07-implementation-strategies.md) 

[Back to top](#top)