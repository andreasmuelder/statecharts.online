---
title: Composite States – Organizing Behavior with Hierarchy
date: 2025-09-22
last_modified_at: 2025-09-22
layout: chapter
description: Use composite states to structure complex behavior with hierarchy, keeping large statecharts readable and maintainable.
faqs:
  - question: What is a composite state?
    answer: >
      A state that contains its own nested region with substates and transitions
      — effectively a statechart within a state.
  - question: When should I use composite states?
    answer: >
      Use them for multi-step processes, grouping related behavior, and
      structuring large charts into readable, reusable modules.
  - question: How do entry and exit behave for composite states?
    answer: >
      Entering starts the nested region at its entry point; exiting leaves all
      active substates before exiting the composite itself.
  - question: Can composite states model modes?
    answer: >
      Yes. Encapsulate mode-specific behavior (e.g., Manual vs MotionSensing)
      as separate composite states.
---

As your statechart grows, you'll likely end up with more and more states. Things can get messy fast — diagrams become hard to read, logic gets harder to follow, and reuse becomes difficult.

This is where **composite states** come in. They let you group related states together into one logical unit and build **hierarchical** statecharts that scale much better as complexity increases.

---

## What is a Composite State?

A **composite state** is a state that contains a nested region with its own substates and transitions. You can think of it like a mini-statechart embedded inside another state.

When the composite state is active, it delegates control to its internal region. This helps organize logic, isolate complexity, and reuse patterns across different parts of your system.

Composite states are useful when:
- A state represents a multi-step process
- You want to group related states with common behavior
- You need to split up a large, flat statechart into manageable pieces
- You want to create reusable sub-behaviors or modes

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

By encapsulating the behavior of the lighting system in separate modes, the statechart becomes easier to read and understand. Each composite state can be viewed as a self-contained module that handles specific functionality, such as manual control or motion sensing.

 <iframe src="https://play.itemis.io?model=8abee2f2-e76d-456d-83d5-87eab96b0475" width="100%" height="800px" style="border: 1px solid" allowfullscreen></iframe>

---

## Summary

Composite states give your statecharts **structure**, **clarity**, and **scalability**. They help you:

- Avoid flat, hard-to-read diagrams
- Encapsulate behavior cleanly
- Control how behavior begins and ends using **entry** and **exit** points
- Model modes and processes in a clear and reusable way

In the next chapter, we'll explore **orthogonal states** — another powerful way to manage complexity by running states *in parallel*.

---

{%- include faq.html -%}

---


Ready to continue? Head over to [Chapter 5: Orthogonal States – Modeling Concurrency Cleanly](05-orthogonal-states.md) 

[Back to top](#top)