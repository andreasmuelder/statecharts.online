# Chapter 8: Implementation Strategies for State Machines

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

The most common and classic approach, it looks like this in `C` code:

```c
enum State { Off, On } state = Off;
int onCount = 0;
bool isOn = false;

void handleEvent(Event event) {
    switch (state) {
        case Off:
            if (event == buttonPressed) {
                isOn = true;            // transition action
                onCount += 1;           // entry action
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

In this approach, the entire state machine is declared **declaratively** in a table.

```c
typedef struct {
    State current;
    Event trigger;
    State next;
    void (*action)();
} Transition;

void toOn()  { isOn = true; onCount += 1; }
void toOff() { isOn = false; }

Transition table[] = {
    { Off, buttonPressed, On,  toOn },
    { On,  buttonPressed, Off, toOff },
};

void handleEvent(Event event) {
    for (int i = 0; i < NUM_TRANSITIONS; ++i) {
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
- Difficult to extend with complex behavior (e.g., actions on transitions)
- Harder to visualize complex flows
- Harder to understand by reading the code

---

### State Pattern (Object-Oriented)

This uses classic object-oriented design:

```cpp
class State {
public:
    virtual void onButtonPressed(Context& ctx) = 0;
};

class OffState : public State {
public:
    void onButtonPressed(Context& ctx) override {
        ctx.isOn = true;
        ctx.onCount++;
        ctx.setState(new OnState());
    }
};

class OnState : public State {
public:
    void onButtonPressed(Context& ctx) override {
        ctx.isOn = false;
        ctx.setState(new OffState());
    }
};

class Context {
public:
    int onCount = 0;
    bool isOn = false;
    State* state = new OffState();

    void setState(State* s) { delete state; state = s; }
    void buttonPressed() { state->onButtonPressed(*this); }
};
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

Popular frameworks like [Spring StateMachine](https://spring.io/projects/spring-statemachine), [Boost MSM](https://www.boost.org/doc/libs/release/libs/msm/doc/HTML/index.html), and [Qt State Machine Framework](https://doc.qt.io/qt-6/statemachine-api.html) provide:
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
<scxml version="1.0" initial="Off" xmlns="http://www.w3.org/2005/07/scxml">
  <datamodel>
    <data id="onCount" expr="0"/>
    <data id="isOn" expr="false"/>
  </datamodel>

  <state id="Off">
    <transition event="buttonPressed" target="On">
      <script>isOn = true;</script>
    </transition>
  </state>

  <state id="On">
    <onentry>
      <script>onCount = onCount + 1;</script>
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
- **Code-only** is great when you generate code from tools like [itemis CREATE](https://create.itemis.io).
- **Library approaches** are ideal for larger, manually maintained projects.
- **Interpreters** are powerful when behavior needs to change dynamically during runtime, also supported by [itemis CREATE](https://create.itemis.io)

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

Ready to continue? Head over to [Chapter 8: Summary and Next Steps](08-summary-and-next-steps.md) 