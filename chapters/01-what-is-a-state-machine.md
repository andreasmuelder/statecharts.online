---
title: What are Statecharts and State Machines?
layout: chapter
description: A friendly introduction to state machines and statecharts with practical motivation and a simple example to build intuition.
faqs:
  - question: What is a state machine?
    answer: >
      A state machine models behavior as a set of named states and transitions
      triggered by events or conditions. It defines how a system moves from one
      situation to another based on inputs.
  - question: How is a statechart different from a state machine?
    answer: >
      A statechart extends a basic state machine with powerful features like
      hierarchy, concurrency, history, and guards—making complex behavior easier
      to model and maintain.
  - question: Why should I use state machines?
    answer: >
      They make complex behavior explicit, testable, and maintainable. You model
      how the system reacts to events instead of scattering logic across
      conditionals and callbacks.
  - question: What are the basic building blocks?
    answer: >
      States represent situations like `Idle` or `Running`, transitions define
      when to move between states, and events trigger those transitions.
---

State machines are one of those concepts that sound more complicated than they really are. Many developers shy away from using them, partly because they're often introduced through heavy academic language—terms like "deterministic automata," "powerset construction," or "mathematical model of computation." That's enough to scare off anyone who's just trying to build software that works.

The good news? You don't need a PhD in computer science to understand or use state machines. In fact, state machines (or *statecharts*, as they're often called in modern tools) are a simple and powerful way to model behavior in many kinds of systems. They're especially useful for reactive systems—systems that respond to external events like button presses, sensor triggers, or user input. Think of elevators, washing machines, or even interactive UIs.

Once you start using state machines, you'll realize they're not some abstract, academic concept—they're a practical and readable way to design systems that respond to real-world events.

In the next chapters, we'll explore how to model richer behavior using powerful concepts like hierarchy, modes, conditions, and events—without getting lost in theory.

Let's keep it simple, visual, and focused on solving real problems.


At their core, state machines are made up of **states** and **transitions**.

- A **state** is just a named situation the system can be in—like `Idle`, `Running`, or `Error`.
- A **transition** defines when and how the system moves from one state to another, usually triggered by some kind of input or condition.

There is always one **initial state** where the system starts, and from there, it can move between states based on what's happening in the world around it.

Let's take a very basic example: a light switch.

---

### Example: A Simple Light Switch

 <iframe src="https://play.itemis.io?model=7ec86474-66d1-4cca-bb60-6f7d91e9601d" width="100%" height="400px" style="border: 1px solid" allowfullscreen></iframe>


> [!TIP]
> You can use the [itemis CREATE Player](https://create.itemis.io) to interactively simulate your statechart. Watching your model run step-by-step is one of the best ways to understand its execution semantics and behavior.

We model the switch with two states:
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

State machines also make it easier to simulate, test, and even generate code for your system — especially when using tools like [itemis CREATE](https://www.itemis.com/en/yakindu/state-machine/), which let you model, simulate, and export code for your charts for different target platforms like C/C++, Java or Python.

---

{%- include faq.html -%}

---


Ready to continue? Head over to [Chapter 2: States, Transitions, and Events](02-states-transitions-and-events.md) 

[Back to top](#top)