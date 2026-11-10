---
title: State Machine Implementation Strategies – Code Patterns and Best Practices
date: 2025-09-22
last_modified_at: 2026-11-10
layout: chapter
reading_time: 15 min
description: Compare code-only, library, and interpreter approaches for implementing state machines, with pros, cons, and examples.
faqs:
  - question: What are the main implementation strategies for statemachines?
    answer: >
      Code-only (switch/case or transition tables), library-based frameworks,
      and interpreter-based approaches like SCXML.
  - question: Which approach is fastest and smallest?
    answer: >
      Code-only implementations are typically fastest with the
      smallest memory footprint.
  - question: When should I choose a library?
    answer: >
      For faster development, standardized semantics, and maintainability in
      larger projects.
  - question: When do I need an interpreter?
    answer: >
      When state machines must be dynamic at runtime and defined in data formats
      like SCXML.
---

Once you've modeled your statechart, the next step is bringing it to life: **implementing** it as real, executable software.  
There are several strategies to do this — each with their own strengths, weaknesses, and typical use cases.

Choosing the right strategy depends on:
- Target platform and language
- Memory constraints (ROM, RAM)
- Runtime performance needs
- Debugging and maintenance requirements
- Whether dynamic behavior (changing statecharts at runtime) is needed

Let’s explore the major approaches based on the following example statechart.

 <iframe src="https://play.itemis.io?model=fcd0ae75-6ec0-4cb9-9597-d5e8615aa9d9" width="100%" height="400px" style="border: 1px solid" allowfullscreen></iframe>

---

## Code-Only Approaches

Code-only implementations don’t rely on any external libraries. They are fast, lightweight, and highly optimized — but also harder to maintain by hand.

### Switch/Case Implementation

The most common and classic approach looks like this in `C` code:

```c
typedef enum { Off, On } State;
typedef enum { buttonPressed } Event;

State state = On;        // initial state
int counter = 1;         // entry action of On ran when the machine started
bool isOn = true;

void handleEvent(Event event) {
    switch (state) {
        case Off:
            if (event == buttonPressed) {
                isOn = true;            // transition action
                counter += 1;           // entry action of On
                state = On;
            }
            break;
        case On:
            if (event == buttonPressed) {
                isOn = false;           // transition action
                state = Off;
            }
            break;
    }
}
```

- Each **state** corresponds to a `case` in a `switch` block.
- Events trigger transitions by executing the corresponding code.
- Orthogonal (parallel) states are managed manually, often using active state vectors.

✅ Advantages:
- Extremely fast
- Minimal memory footprint
- No external dependencies

❌ Disadvantages:
- Hard to read for large statecharts
- Difficult to maintain and extend manually
- Risk of inconsistencies without automated tooling

---

### State Transition Table

In this approach, the entire state machine is described **declaratively** in a table. It uses the same declarations as above.

```c
typedef struct {
    State current;
    Event trigger;
    State next;
    void (*action)(void);
} Transition;

void toOn(void)  { isOn = true; counter += 1; }
void toOff(void) { isOn = false; }

Transition table[] = {
    { Off, buttonPressed, On,  toOn },
    { On,  buttonPressed, Off, toOff },
};

#define NUM_TRANSITIONS (sizeof(table) / sizeof(table[0]))

void handleEvent(Event event) {
    for (size_t i = 0; i < NUM_TRANSITIONS; ++i) {
        if (table[i].current == state && table[i].trigger == event) {
            table[i].action();
            state = table[i].next;
            break;
        }
    }
}
```

The logic is completely separated from the state machine structure. Transitions are just data. Actions are function pointers that can be reused or extended.

✅ Advantages:
- Good separation between logic and execution
- Easy to generate automatically

❌ Disadvantages:
- Difficult to extend with complex behavior (e.g., hierarchy or history)
- Harder to visualize complex flows
- Harder to understand by reading the code

---

### State Pattern (Object-Oriented)

This uses classic object-oriented design:

```cpp
class Context;

class State {
public:
    virtual ~State() = default;
    virtual void onButtonPressed(Context& ctx) = 0;
};

class OffState : public State {
public:
    void onButtonPressed(Context& ctx) override;
};

class OnState : public State {
public:
    void onButtonPressed(Context& ctx) override;
};

class Context {
public:
    int counter = 1;         // entry action of On ran when the machine started
    bool isOn = true;
    OffState off;
    OnState on;

    void setState(State& next) { state = &next; }
    void buttonPressed() { state->onButtonPressed(*this); }

private:
    State* state = &on;      // initial state
};

void OffState::onButtonPressed(Context& ctx) {
    ctx.isOn = true;         // transition action
    ctx.counter++;           // entry action of On
    ctx.setState(ctx.on);
}

void OnState::onButtonPressed(Context& ctx) {
    ctx.isOn = false;        // transition action
    ctx.setState(ctx.off);
}
```

- Each **state** is a class that implements a common interface.
- Transitions happen by changing the current active object.

✅ Advantages:
- Very readable and modular
- Easy to extend new states or behaviors

❌ Disadvantages:
- Higher memory usage
- More overhead compared to raw code

---

## Library-Based Approaches

Popular frameworks like [Spring StateMachine](https://spring.io/projects/spring-statemachine), [Boost MSM](https://www.boost.org/libs/msm/), and [Qt State Machine Framework](https://doc.qt.io/qt-6/qtstatemachine-index.html) provide:
- Clear and well-tested execution semantics
- Easier declaration of states, events, and transitions
- Built-in support for hierarchy, concurrency, time events, etc.

✅ Advantages:
- Faster development
- Easier debugging and maintenance
- Standardized behavior across projects

❌ Disadvantages:
- Adds external dependencies
- May be too heavy for ultra-lightweight embedded systems

---

## Interpreter-Based Approaches

Interpreters allow **dynamic** state machines that can be loaded, modified, or replaced at runtime.

Typically, these models are defined in a **data format** like [SCXML (State Chart XML)](https://www.w3.org/TR/scxml/), a W3C standard.

Here is an example of our statemachine in SCXML:

```xml
<scxml version="1.0" initial="On" datamodel="ecmascript" xmlns="http://www.w3.org/2005/07/scxml">
  <datamodel>
    <data id="counter" expr="0"/>
    <data id="isOn" expr="true"/>
  </datamodel>

  <state id="Off">
    <transition event="buttonPressed" target="On">
      <script>isOn = true;</script>
    </transition>
  </state>

  <state id="On">
    <onentry>
      <script>counter = counter + 1;</script>
    </onentry>
    <transition event="buttonPressed" target="Off">
      <script>isOn = false;</script>
    </transition>
  </state>

</scxml>
```


✅ Advantages:
- Full dynamic flexibility
- Platform-independent modeling
- Great for highly dynamic systems

❌ Disadvantages:
- Higher runtime cost
- More complex implementations
- SCXML models can be verbose and harder for humans to manage manually

---

## Choosing the Right Strategy

| Goal/Constraint         | Recommended Strategy           |
|--------------------------|---------------------------------|
| Ultra-small, fast code   | Code-only (Switch/Case or Table) |
| Easy development & maintenance | Library (Spring, Boost, RKH) |
| Runtime dynamic models   | Interpreter (SCXML engines)     |
| Static embedded systems  | Code-only or lightweight library |

In practice:
- **Code-only** is great when you generate code from tools like [itemis CREATE](https://create.itemis.io/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch08-implementation).
- **Library approaches** are ideal for larger, manually maintained projects.
- **Interpreters** are powerful when behavior needs to change dynamically during runtime, also supported by [itemis CREATE](https://create.itemis.io/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch08-implementation).

---

## Summary

There is no single "best" way to implement a state machine — only the best way for your specific project.

Key questions to ask:
- Do I need ultimate speed and minimal memory?
- Do I need runtime flexibility?
- Is long-term maintainability a priority?
- Am I generating code automatically or writing it manually?

By understanding the trade-offs, you can choose the right approach and build reliable, elegant systems based on the timeless power of state machines.

---

{%- include faq.html -%}

---

<div style="display: flex; justify-content: space-between; width: 100%; align-items: center;">
  <div style="text-align: left; flex: 2;"><a href="07-execution-semantics.html">← Previous: Execution Semantics</a></div>
  <div style="text-align: center; flex: 1;"><a href="#top">Back to top</a></div>
  <div style="text-align: right; flex: 2;"><a href="09-tools-and-frameworks.html">Next: Tools and Frameworks →</a></div>
</div>