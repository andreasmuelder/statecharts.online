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

Ready to continue? Head over to [Chapter 4: Composite States – Organizing Behavior with Hierarchy](04-composite-states.md) 