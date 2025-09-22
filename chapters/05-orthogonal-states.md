---
title: Orthogonal States – Modeling Concurrency Cleanly
date: 2025-09-22
last_modified_at: 2025-09-22
layout: chapter
description: Model logical concurrency with orthogonal regions to run independent behaviors side-by-side without threads.
faqs:
  - question: What are orthogonal states?
    answer: >
      Composite states with multiple regions that execute logically in parallel.
      Each region has its own substates and transitions.
  - question: Is this true parallel execution?
    answer: >
      No. Execution is sequential per cycle. Orthogonality models logical
      concurrency without threads or synchronization primitives.
  - question: When should I use orthogonal states?
    answer: >
      When modeling independent subsystems that operate side-by-side, such as
      fan and temperature control in a smart home.
  - question: How do regions coordinate?
    answer: >
      Use events, shared variables, or synchronization mechanisms to communicate
      between regions as needed.
---

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
- In a smart home system, one region controls the fan, another handles the temperature.
- In a vehicle, one region manages cruise control, another handles lane keeping.

Each part has its own logic, but they run **together** as long as the parent orthogonal state is active.

---

## Example: Smart Home Temperature and Fan Control

This statechart represents a system with two independent control mechanisms: one for temperature and one for fan operation. Each control mechanism can toggle between two states based on specific events (toggleTemp and toggleFan). The use of orthogonal regions allows the temperature and fan controls to operate independently, reflecting a design where both systems can be managed concurrently without interference.

 <iframe src="https://play.itemis.io?model=3d020105-46b4-4572-9be6-8883cf13aad2" width="100%" height="600px" style="border: 1px solid" allowfullscreen></iframe>

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

---

{%- include faq.html -%}

---


Ready to continue? Head over to [Chapter 6: Final States, Choices, and History](06-final-states-choices-and-history.md) 

[Back to top](#top)