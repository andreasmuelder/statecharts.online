---
title: Variables – Giving Your Statechart a Memory
layout: chapter
---

Statecharts become truly powerful when they can *remember* things — and that's exactly what variables allow them to do.

With variables, your statechart can:
- Count how many times something happened
- Track status or mode
- Store values like user input, sensor readings, or error codes
- Make decisions based on internal data

In short, variables give your statechart context — they turn simple reactions into intelligent behavior.

---

## What is a Variable?

A **variable** is a named container for a value. In some tools it has a type (like number, string, or boolean) and it can be used in:
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

 <iframe src="https://play.itemis.io?model=fcd0ae75-6ec0-4cb9-9597-d5e8615aa9d9" width="100%" height="400px" style="border: 1px solid" allowfullscreen></iframe>

This statechart models a simple light switch mechanism with two states: On and Off. It utilizes two variables, isOn and counter, to manage the state transitions and track the number of times the light is turned on.

Each time the light is turned on, the counter increases. When entering the `On` state, the `entry` action increases the variable `counter`. When the `buttonPressed` event is raised, the transition sets the variable `isOn` to true or false. 

---

## Summary

Variables are the memory of your statechart. Without them, you can only model behavior in terms of "what just happened." With variables, you can also model **what has happened before**, and **what is currently true**.

They help you:
- Build smarter, more responsive behavior
- Store internal state cleanly
- Avoid cluttering your chart with unnecessary states

> [!TIP]
> If you find yourself duplicating similar states just to track a number or flag — it might be time to use a variable instead.


In the next chapter, we'll explore how statecharts grow in complexity, and how hierarchy and parallelism help manage that complexity in a clean and scalable way.

Ready to continue? Head over to [Chapter 4: Composite States – Organizing Behavior with Hierarchy](04-composite-states.md) 