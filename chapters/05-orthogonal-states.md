---
title: Orthogonal States in Statecharts – Modeling Parallel Behavior and Concurrency
date: 2025-09-22
last_modified_at: 2025-09-22
layout: chapter
reading_time: 10 min
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
      Use events, shared variables, or synchronization mechanisms like join
      and fork nodes to communicate between regions as needed.
  - question: What are join and fork nodes?
    answer: >
      Fork nodes split execution into multiple parallel paths, while join nodes
      synchronize multiple parallel paths back together before proceeding.
  - question: When should I use join/fork?
    answer: >
      Use fork to start multiple parallel activities simultaneously, and join
      to wait for multiple parallel activities to complete before proceeding.
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

## Join and Fork: Advanced Synchronization

For more sophisticated coordination between orthogonal regions, statecharts provide **join** and **fork** nodes. These special synchronization constructs allow you to:

- **Split execution** into multiple parallel paths (fork)
- **Synchronize and merge** multiple parallel paths back together (join)

### Fork Nodes

A **fork node** (also called a fork bar) splits a single transition into multiple parallel transitions, activating several regions simultaneously. It's represented by a thick black bar with one incoming transition and multiple outgoing transitions.

Fork nodes are useful when:
- You need to start multiple parallel activities at once
- An event should trigger several independent processes
- You want to coordinate the start of orthogonal regions

### Join Nodes

A **join node** (also called a join bar) waits for multiple parallel regions to reach specific states before proceeding. It's represented by a thick black bar with multiple incoming transitions and one outgoing transition.

Join nodes are useful when:
- You need to wait for multiple parallel activities to complete
- Several conditions must be met before proceeding
- You want to synchronize the end of orthogonal regions

### Example: Embedded System Initialization

Consider an embedded IoT device that must initialize two subsystems before it becomes operational:

 <iframe src="https://play.itemis.io?model=0b11d9ca-e794-4f7f-83af-4487ede3f1ce" width="100%" height="600px" style="border: 1px solid" allowfullscreen></iframe>


The fork ensures all initialization processes start simultaneously when the device powers on. The join ensures the system only transitions to "Operational" state when **all** subsystems have successfully initialized.

This pattern is common in embedded systems where:
- Network connectivity, sensor calibration, and security setup can happen in parallel
- The system must not accept user commands until all subsystems are operational
- Failure in any subsystem should prevent the device from becoming ready

### Execution Semantics

- **Fork execution**: When a fork is triggered, all outgoing transitions are taken simultaneously, activating their target states in parallel
- **Join execution**: A join transition is only enabled when **all** incoming transitions are enabled (i.e., all prerequisite states are active)

This provides deterministic synchronization without the complexity of traditional threading models.

---

## Summary

Orthogonal states are a powerful tool for modeling **concurrent, but independent** behaviors within a single system.

They help you:
- Keep related concerns separated
- Model multiple control flows cleanly
- Avoid complex nesting by distributing logic across parallel regions
- Coordinate parallel activities using **join and fork** synchronization

Advanced synchronization with join and fork nodes enables you to:
- Split single transitions into multiple parallel paths
- Synchronize multiple parallel activities before proceeding
- Create deterministic coordination patterns without threading complexity

Just remember: orthogonality is **not true parallelism**. It's a way to think about things happening side by side — while still executing in a **defined, sequential order**.

In the next chapters, we'll look at more advanced features like **history states**, **choice nodes**, and **final states**, which add even more power to your modeling toolbox.

---

{%- include faq.html -%}

---

<div style="display: flex; justify-content: space-between; width: 100%; align-items: center;">
  <div style="text-align: left; flex: 2;"><a href="04-composite-states.html">← Previous: Composite States</a></div>
  <div style="text-align: center; flex: 1;"><a href="#top">Back to top</a></div>
  <div style="text-align: right; flex: 2;"><a href="06-final-states-choices-and-history.html">Next: Final States, Choices, and History →</a></div>
</div>