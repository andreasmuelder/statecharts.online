---
title: Testing State Machines – Unit Tests and the Limits of Testing
date: 2026-11-10
last_modified_at: 2026-11-10
layout: chapter
reading_time: 12 min
description: Learn how to unit test state machines and statecharts, what to cover, how to handle time and external operations, and where formal methods find the bugs that tests miss.
faqs:
  - question: How do you unit test a state machine?
    answer: >
      Start the machine, feed it a sequence of events (and, if needed, advance
      time), then check the result after each step through its interface:
      variable values, outgoing events, and calls to external operations.
  - question: What is the difference between black-box and white-box testing of a state machine?
    answer: >
      A black-box test talks to the state machine only through its interface:
      it raises events and checks variables and outgoing events. A white-box
      test also looks inside, for example at which state is active. Black-box
      tests survive refactoring; white-box tests break when the internal
      structure changes.
  - question: How do I test timed transitions without waiting?
    answer: >
      Run the machine against a virtual clock that the test controls. Advancing
      the clock by an hour fires all affected time events instantly, so the test
      stays fast and deterministic.
  - question: What is the difference between testing and formal verification?
    answer: >
      A test checks the scenarios you wrote down. Formal methods analyse all
      possible behaviors of the model and can prove that a problem, such as an
      unreachable transition, exists or does not exist. Tests show intended
      behavior; formal analysis finds whole classes of logical errors.
---

A statechart that looks right in the simulator is a good start. But clicking through a model by hand only shows that it works for the paths you clicked — today. It doesn't tell you whether it still works after the next change.

The good news: state machines are unusually easy to test. In this chapter, you'll learn:

- What a **state machine test** looks like
- Why good tests treat the state machine as a **black box**
- **What to test** beyond the happy path
- How to deal with **time** and the **outside world**
- Where testing ends and **formal methods** begin

---

## What a State Machine Test Looks Like

Almost every state machine test follows the same three steps:

1. **Enter** the state machine, so it takes its initial state
2. **Stimulate** it by raising events or letting time pass
3. **Check** the result once the step is complete: variable values, outgoing events

Let's use the light switch from [Chapter 3](03-variables.html). It starts switched on, toggles between `On` and `Off`, and counts how often the light was turned on.

 <iframe src="https://play.itemis.io?model=fcd0ae75-6ec0-4cb9-9597-d5e8615aa9d9" width="100%" height="400px" style="border: 1px solid" allowfullscreen></iframe>

Try it yourself: predict the values of `isOn` and `counter` after three `buttonPressed` events, then raise the event three times and compare. Congratulations, you just ran a test case by hand.

An automated test writes the same thing down, so a machine can repeat it after every change:

```text
test "counts each time the light is turned on":
    enter
    expect isOn == true
    expect counter == 1

    raise buttonPressed
    expect isOn == false
    expect counter == 1

    raise buttonPressed
    expect isOn == true
    expect counter == 2
```

Notice that the test checks more than "the light is on". It also makes sure that switching the light *off* does not increase the counter.

### The Same Test in Code

The notation above is pseudocode. You don't need special tooling to turn it into a real test — the unit test framework you already use will do. Here is the light switch and its test in plain Python:

```python
import unittest


class LightSwitch:
    def __init__(self):
        self._state = "On"         # internal: the light starts switched on
        self.is_on = True
        self.counter = 1

    def button_pressed(self):
        if self._state == "Off":
            self._state = "On"
            self.is_on = True
            self.counter += 1
        else:
            self._state = "Off"
            self.is_on = False


class LightSwitchTest(unittest.TestCase):
    def test_counts_each_time_the_light_is_turned_on(self):
        light = LightSwitch()
        self.assertTrue(light.is_on)
        self.assertEqual(light.counter, 1)

        light.button_pressed()
        self.assertFalse(light.is_on)
        self.assertEqual(light.counter, 1)

        light.button_pressed()
        self.assertTrue(light.is_on)
        self.assertEqual(light.counter, 2)


if __name__ == "__main__":
    unittest.main()
```

The structure is the same in GoogleTest, JUnit, or any other framework. Many modeling tools also offer a test framework of their own, such as SCTUnit, with a test language that reads much like the pseudocode above and runs directly against the model, before any code exists.

---

## Black Box or White Box?

Look at the tests above again. They never ask which state is active. They only use what the state machine offers to the outside:

- **Events** that go in
- **Variables** and **outgoing events** that come out

This is the **interface** of the state machine, and a test that sticks to it is a **black-box test**: raise an event, let the run-to-completion step finish (see [Chapter 7](07-execution-semantics.html)), then check the result.

A **white-box test** also looks inside:

```text
raise buttonPressed
expect active state On
```

That is tempting, because the states are right there in the diagram. But which state is active is an internal detail. The rest of the system never sees it — it only sees that the lamp is on.

| | Black-box test | White-box test |
|---|---|---|
| Uses | Events, variables, outgoing events | Also active states and other internals |
| Checks | What the state machine **does** | How it is **built** |
| After a refactoring | Still passes | May fail although the behavior is unchanged |
| Works for | Model, generated code, handwritten code | Only where the internals are accessible |

The third row matters most. If you group two states into a composite state or merge two twin states (see [Chapter 11](11-refactoring-statecharts.html)), the behavior stays the same. Black-box tests stay green and confirm it. White-box tests turn red, and you spend your time fixing tests instead of trusting them.

White-box checks still have their place: they help you find out *why* a test fails, and some tools let you assert on active states directly. Use them sparingly.

> 💡 **Tip:** If you can only test a behavior by asking for the active state, the interface is probably missing something. Add a variable or an outgoing event — the rest of your system will need it too.

---

## What to Test

A good test suite covers more than the main path. Use this list as a starting point:

- **The start**: after entering, do variables and outputs have their initial values?
- **Every transition**: each one should fire in at least one test
- **Guards on both sides**: for `[counter < 5]`, test the values 4 and 5
- **Events that must be ignored**: raise an event in a state that should not react, and check that nothing changed
- **Outgoing events and actions**: is the right event raised, and only once?
- **Composite states**: a transition leaving a composite state must work from each substate, and a history state must restore the right one
- **Orthogonal regions**: test the cases where one region influences another

> 💡 **Tip:** Bugs love boundaries. If a guard compares against a number, always test the value just below and just above it.

---

## Testing Time Without Waiting

Many statecharts depend on time. A light blinks every 400 ms; a device goes to standby after 30 minutes. A test that really waits 30 minutes is not a test anyone will run.

The solution is **virtual time**. The test owns the clock. When it moves the clock forward, all time events that became due fire immediately:

```text
test "goes to standby after 30 minutes":
    enter
    raise buttonPressed
    advance time by 30 min
    expect displayOn == false
```

This test finishes in milliseconds and gives the same result on every run. If you write state machine code by hand, the rule is: never read the system clock inside the state machine. Pass time in from outside, so a test can replace it. Tools like itemis CREATE provide such a time abstraction, with a [virtual implementation](https://www.itemis.com/en/products/itemis-create/documentation/user-guide/testing-state-machines/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch10-testing#sctunit_proceeding_a_state_machine) that can be used in tests.

---

## Isolating the Outside World

A state machine rarely lives alone. It reads sensors, starts motors, and sends messages. When you test the *logic*, you don't want real hardware involved. Two techniques help:

- **Mocking**: the test decides what an operation returns, so you can steer the machine into every branch
- **Call checks**: the test verifies that an operation was called

```text
test "shuts down when too hot":
    given readTemperature returns 95.0
    enter
    raise tick
    expect stopHeating was called
    expect event alarm was raised
```

---

## Model Tests and Code Tests

You can test a state machine at two levels:

| | Model tests | Code tests |
|---|---|---|
| What is tested | The statechart, run by a simulator | The generated or handwritten code |
| Question | Is the **logic** right? | Does the **implementation** behave like the logic? |
| When | As soon as the model exists | Once there is code |

If you write the state machine by hand, there is only the code level — and that's fine. If you generate code from a model, both levels work best together. Some tools can even generate code tests from model tests, so the same test cases run against both.

Testing also works *before* modeling. Write down a requirement as a test, watch it fail, then extend the model until it passes. This test-first approach keeps models small, because every state exists for a reason. The article [Model-Driven Software Development meets Test-Driven Development](https://www.itemis.com/en/blog/model-based-engineering/model-driven-software-development/mdd-meets-tdd/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch10-testing) walks through it step by step.

---

## Beyond Testing – Formal Methods

A test checks one path with one set of values. Some bugs hide in a model in a way that no test will ever reveal — unless you already suspect them. As Edsger Dijkstra put it: testing can show the presence of bugs, but never their absence.

**Formal methods** take the opposite approach. Instead of running selected scenarios, they analyze the model mathematically for *all* possible values and paths. Typical findings are:

- **Contradictory guards**: `[counter > 10 && counter < 5]` can never be true, so the transition never fires
- **Tautological guards**: a guard that is always true is redundant, and often a typo
- **Shadowing**: two transitions react to the same event, and the one with higher priority always wins
- **Dead states**: a state that can be entered but never left
- **Unreachable states**: no sequence of events leads there
- **Division by zero**: a divisor that can become zero on some path

Two families of techniques are common:

- **Symbolic execution with an SMT solver** works with symbols instead of concrete values and asks a solver whether a path is possible at all. It is strong on data: guards, arithmetic, value ranges. Simple checks are fast enough to run in the editor while you model.
- **Model checking** with tools like [SPIN](https://spinroot.com/spin/whatispin.html) or [UPPAAL](https://uppaal.org/) explores the state space and checks properties over time, such as "both valves are never open at the same time". It is strong on concurrency and timing, but usually needs a translation of the model and abstracts data away.

Both hit the same wall: **state explosion**. Every branch can double the number of paths, and every loop can create infinitely many. Practical tools therefore cut loops or limit depth, trading some precision for a guaranteed result.

### Testing vs. Formal Methods

| | Testing | Formal methods |
|---|---|---|
| Checks | The scenarios you wrote down | All paths of the model |
| Finds | Behavior that violates a requirement | Logical errors like unreachable or dead elements |
| Knows the requirements? | Yes, they are in the tests | No |
| Main limit | Covers only what you thought of | State explosion |

The two answer different questions, and neither replaces the other. A formal analysis can prove that a transition is unreachable, but it can't know that the light *should* have turned on. A test knows what should happen — but only for the cases you wrote.

Want to go deeper? [Formal Error Detection on State Machines: What Works and What Doesn't](https://www.itemis.com/en/blog/model-based-engineering/model-driven-software-development/formal-methods-error-detection/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch10-testing) reports on two master's theses that built such an analysis for statecharts, including what failed and where the limits are.

---

## Summary

Testing turns "it worked when I clicked through it" into something you can rely on:

- A test **enters** the machine, **stimulates** it, and **checks** the result after each step
- Good tests are **black-box**: they use events, variables, and outgoing events — not the active state
- Test guards at their boundaries and events that must be ignored
- Use **virtual time** and **mocks** to keep tests fast and independent of hardware
- **Formal methods** find logical errors that tests miss, but they don't know your requirements

Everything in this chapter works with a plain unit test framework. Tools differ in how much they take off your hands, which is why [Chapter 9](09-tools-and-frameworks.html) lists testing as a selection criterion. To see what such a framework looks like in practice, have a look at SCTUnit in the [testing chapter of the itemis CREATE documentation](https://www.itemis.com/en/products/itemis-create/documentation/user-guide/testing-state-machines/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch10-testing).

---

{%- include faq.html -%}

---

<div style="display: flex; justify-content: space-between; width: 100%; align-items: center;">
  <div style="text-align: left; flex: 2;"><a href="09-tools-and-frameworks.html">← Previous: Tools and Frameworks</a></div>
  <div style="text-align: center; flex: 1;"><a href="#top">Back to top</a></div>
  <div style="text-align: right; flex: 2;"><a href="11-refactoring-statecharts.html">Next: Refactoring Statecharts →</a></div>
</div>
