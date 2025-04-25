---
title: Final States, Choices, and History – Controlling Flow and Remembering State
layout: chapter
---

As you model more complex behavior in a statechart, you'll need additional tools to control how execution flows and how the system "remembers" what it was doing. In this chapter, we introduce three important concepts that extend the power of your statecharts:

- **Final states** to indicate completion
- **Choice nodes** to make conditional decisions
- **History states** to remember previously active states

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

## Choice Nodes – Making Conditional Decisions

A **choice node** is a pseudo-state that lets you branch conditionally after a transition.

Here's how it works:
- A transition leads into a choice node
- Multiple transitions lead out, each with a guard condition
- The first transition whose guard evaluates to `true` is taken
- If no guard matches, a **default transition** should be provided (using a trigger like `else` or `default`)

This pattern is useful when the system needs to evaluate data and choose between multiple paths.

### Example:

Imagine a quiz system that branches depending on the score:
- If `score >= 80`, go to `PassWithDistinction`
- If `score >= 50`, go to `Pass`
- Otherwise, go to `Fail`

A choice node makes this logic explicit and easy to model.

---

## History States – Picking Up Where You Left Off

A **history state** allows a composite state to remember which substate was active the last time it was exited. The next time the composite state is entered, it resumes at that remembered state instead of starting from the beginning.

There are two types of history states:
- **Shallow history**: remembers the active state **one level deep**
- **Deep history**: remembers the active state **recursively**, including any nested composite states

History states are useful when:
- You want to **pause and resume** processes
- You want to **save user progress**
- You want to avoid repeating already completed steps

### Shallow History Example

Imagine a form with multiple steps inside a composite state called `answeringQuestions`. At any time, the user can press "Pause", which returns the system to an outer state like `Init`. Later, when they hit "Continue", the system resumes **exactly where they left off** — thanks to the shallow history state.

Shallow history tracks only the most recent active substate in that region.

### Deep History Example

If your substates themselves contain **nested composite states**, and you want to resume the entire nested hierarchy, use a **deep history state**. It remembers not just one level, but the full internal configuration.

This is especially useful in systems with complex workflows, multiple levels of modes, or nested user interfaces.

---

## Summary

These advanced flow control tools give you more flexibility and precision in your statecharts:

- **Final states** signal completion
- **Choice nodes** enable dynamic branching
- **History states** preserve execution context across interruptions

Used correctly, they can make your models more robust, interactive, and user-friendly — especially in systems where users pause, resume, retry, or take different paths based on conditions.

In the next chapter, we'll explore how all these concepts come together in real-world patterns and best practices for designing readable, scalable statecharts.

Ready to continue? Head over to [Chapter 7: Summary and Next Steps](chapters/07-summary-and-next-steps.md) 