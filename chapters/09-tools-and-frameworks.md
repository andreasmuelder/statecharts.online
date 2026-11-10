---
title: Best State Machine Tools and Frameworks – Libraries and Modeling Software
date: 2025-09-22
last_modified_at: 2026-11-10
layout: chapter
reading_time: 12 min
description: Overview of popular tools and frameworks for modeling and executing state machines and statecharts across languages and platforms.
faqs:
  - question: How do I choose between a modeling tool and a code library?
    answer: >
      Use a modeling tool when you need simulation/visual debugging, validation/testing, documentation artifacts, and code generation across languages. Use a code library when you want to stay purely in code with lightweight dependencies and full manual control.
  - question: Does SCXML support matter in practice?
    answer: >
      It matters if you need model interchange, standards-based semantics, or to load/modify machines at runtime. If your workflow is tool-specific or code-only, SCXML may not be a requirement.
  - question: Can these tools integrate with handwritten code?
    answer: >
      Yes, but integration depth varies. Some tools support side-by-side development with C/C++/Java and custom headers; libraries integrate directly since you write code around them. Evaluate build tooling and interface boundaries.
  - question: Are there free or academic options?
    answer: >
      itemis CREATE provides free academic licenses; libraries are often open source. Verify current terms on vendor and project sites.
---

Choosing the right tooling can accelerate development, improve reliability, and make complex behavior easier to manage. This chapter highlights widely used libraries, runtimes, and modeling tools you can apply in different contexts. When evaluating options, consider your target platforms and languages, the feature set you actually need (hierarchy, orthogonal regions, timers, history, and a data model), and the quality of tooling for everyday work such as visual debugging, validation, testing, and code generation. Interoperability also matters: support for standards like SCXML, the ability to integrate with handwritten code and existing build systems, and collaboration features such as version control integration and model diff/merge can greatly influence team workflows. Finally, look at operational fit: ease of CI/CD and headless automation, performance and footprint for your runtime environment, licensing and add‑on requirements, and the learning curve for cross‑functional teams.

---

## Quick Checklist

- Define your goal: modeling with simulation/codegen, or code-only library?
- Confirm platform/language fit and long-term ecosystem maturity.
- Check essentials: hierarchy, parallel regions, timers, history, data model.
- Validate workflow: visual debugging, [testing and coverage](10-testing-state-machines.html), traceability, documentation.
- Assess interoperability: SCXML, integration with existing code/build, VCS diff/merge.
- Ensure CI readiness: headless execution, license model for build agents.
- Weigh learning curve and total cost (including required add-ons).

---
## Modeling and Code Generation Tools

Three commercial tools come up again and again when teams model statecharts graphically and generate code from them: MathWorks Stateflow, IBM Engineering Rhapsody, and itemis CREATE. They share a subject but follow very different ideas of what a statechart tool should be. This section is a starting point, not a complete catalog; verify current capabilities for your needs.

> **A note on transparency:** This guide is maintained by the technical lead of itemis CREATE, and the interactive examples run on its player. The comparison below is condensed from a longer article by the product owner of itemis CREATE. The reasoning is laid out so you can judge it yourself.

### Three Tools, Three Philosophies

**[Stateflow](https://www.mathworks.com/products/stateflow.html) (MathWorks)** is an add-on to MATLAB and Simulink. That integration is its greatest strength and its clearest constraint. Teams that already use Simulink for control engineering get simulation and code generation across continuous and discrete parts of one model. Teams that do not need Simulink have to acquire the surrounding stack just to model statecharts.

**[IBM Engineering Rhapsody](https://www.ibm.com/products/engineering-rhapsody)** is a full UML/SysML modeling environment in which state machines are one diagram type among many, next to class, sequence, and activity diagrams and AUTOSAR support. That breadth suits large, specification-driven systems engineering projects. For embedded, real-time capable code it is typically combined with a separate execution framework, which adds cost and integration effort.

**[itemis CREATE](https://www.itemis.com/en/products/itemis-create/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch09-tools)** (formerly YAKINDU Statechart Tools) is built for one job: statechart modeling, simulation, testing, and code generation. Its scope is deliberately narrower than Rhapsody's. It generates C, C++, C#, Java, Python, and SCXML, and runs as a desktop application, a VS Code extension, and a web app.

### Feature Matrix

A high-level comparison to help you shortlist; verify versions and features for your environment.

| Feature | [itemis CREATE](https://www.itemis.com/en/products/itemis-create/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch09-tools) | [Stateflow](https://www.mathworks.com/products/stateflow.html) (MathWorks) | [IBM Rhapsody](https://www.ibm.com/products/engineering-rhapsody) |
|---|---|---|---|
| Typical users | Software developers and architects, mixed teams | Control and system engineers in MATLAB/Simulink workflows | System architects in specification-driven, safety-critical projects |
| Modeling focus | Purpose-built statecharts | Statecharts and block diagrams within Simulink | UML/SysML incl. state machines |
| Platforms | Desktop, web app, VS Code extension | Desktop and web via MATLAB/Simulink | Desktop |
| Hierarchy and orthogonal regions | Yes | Yes | Yes |
| Simulation & visual debugging | Built-in, interactive | Very powerful within the Simulink stack | Available; setup is more involved |
| Code generation | C, C++, C#, Java, Python, SCXML | C/C++ via Simulink Coder / Embedded Coder add-ons | C and C++ with framework-specific patterns |
| SCXML support | Yes | No | No |
| Integration with handwritten code | Deep integration with C/C++ and Java, model and code side by side | External C calls; depends on the Simulink build environment | External C calls; tighter tool coupling |
| Validation, testing, coverage | Live validation; unit testing and coverage in the desktop edition | Design Verifier and Coverage as add-on toolboxes | Model checks, consistency checks, test integrations |
| Collaboration / version control | Git/SVN integration; model diff and merge | Simulink Projects | Centralized multi-user infrastructure |
| Interactive model player for documentation | Yes, embeddable in wikis and requirements tools | No | No |
| CI / headless generation | Command-line generator | Possible; needs MATLAB licenses on build agents | Command-line integration with scripting |
| Extensibility | Open APIs, custom code generators | MATLAB scripts and proprietary extensions | Extensible within the IBM ecosystem |
| Learning curve | Approachable for development teams | Natural for control engineers, steeper for others | High due to UML/SysML breadth |
| Licensing | One license for the full feature set | Modular toolboxes | Enterprise licensing, feature-dependent |

Not every row weighs the same for every team. Testing and coverage, for example, matter a great deal once you work as described in [Chapter 10](10-testing-state-machines.html), and CI support only matters if you generate code in a build pipeline.

### Cost

Feature tables hide that these tools sit in different price tiers, mostly because of what you have to buy around the statechart editor. Stateflow needs MATLAB, Simulink, and a coder product; Rhapsody is usually paired with an execution framework for embedded targets; itemis CREATE is a single subscription. The source article estimates, as of summer 2026 and partly from secondary sources, license costs for three developers over three years of roughly €12,000 for itemis CREATE, €20,000–30,000 for Rhapsody with framework, and at least €45,000 for a full Stateflow stack. Prices change, so check current vendor quotes before deciding.

### Which Tool for Which Context?

- **Choose Stateflow** if your team already lives in MATLAB/Simulink, for example for model-based control design where continuous simulation and discrete state logic must work together. If state machines are your primary need and you start fresh, the full stack is considerable overhead.
- **Choose Rhapsody** if you work on a large systems engineering project that needs the breadth of UML and SysML, and statecharts are one part of a much larger modeling effort.
- **Choose itemis CREATE** if statechart modeling with portable generated code is your primary need, across bare-metal, RTOS-based, JVM, .NET, or scripted targets, and you want a short learning curve and simple CI integration.

The full comparison, with all criteria, per-seat prices, and the reasoning behind each rating, is in [Statechart Tools Compared: MathWorks Stateflow vs. IBM Rhapsody vs. itemis CREATE](https://www.itemis.com/en/blog/model-based-engineering/model-driven-software-development/statechart-tools-comparison-rhapsody-stateflow-itemis-create/?utm_source=statecharts.online&utm_medium=referral&utm_campaign=state-machine-guide&utm_content=ch09-tools).

---

## Statemachine Frameworks (by Ecosystem)

### Java / JVM
- [Spring Statemachine](https://spring.io/projects/spring-statemachine): Mature framework with hierarchical states, regions, guards, actions, events, and persistence.
- [Apache Commons SCXML](https://commons.apache.org/proper/commons-scxml/): SCXML interpreter for the JVM.

### C++
- [Boost MSM](https://www.boost.org/libs/msm/) / [Boost.SML](https://boost-ext.github.io/sml/): Compile-time state machine libraries with strong type-safety and performance.
- [Qt State Machine Framework](https://doc.qt.io/qt-6/qtstatemachine-index.html): Part of Qt; integrates with signals/slots and QML.

### JavaScript / TypeScript
- [XState](https://xstate.js.org/): Full-featured statecharts with visualizer, actors, and tooling.

### C# / .NET
- [Stateless](https://github.com/dotnet-state-machine/stateless): Lightweight C# state machine library with a fluent API.

### Python
- [Sismic](https://sismic.readthedocs.io/) and [transitions](https://github.com/pytransitions/transitions): Declarative state machine libraries with readable APIs.

### Embedded C/C++
- [RKH](https://github.com/vortexmakes/RKH): Lightweight framework with hierarchical states and event-driven kernels.

### SCXML Engines (cross-platform)

SCXML is a W3C standard for state charts. Engines exist across platforms (e.g., [Apache Commons SCXML](https://commons.apache.org/proper/commons-scxml/) for Java, [SCION](https://github.com/jbeard4/SCION) for JavaScript/Web). Choose SCXML when you need model interchange or runtime-loaded machines.

---

## Summary

The right tool depends on your context: platform and language needs, required statechart features, workflow and collaboration tooling, and operational fit for CI and licensing. Use the quick checklist to shortlist candidates, then prototype a core flow to validate ergonomics and integration. When in doubt, favor clarity, testability, and interoperability.

---

{%- include faq.html -%}

---

<div style="display: flex; justify-content: space-between; width: 100%; align-items: center;">
  <div style="text-align: left; flex: 2;"><a href="08-implementation-strategies.html">← Previous: Implementation Strategies</a></div>
  <div style="text-align: center; flex: 1;"><a href="#top">Back to top</a></div>
  <div style="text-align: right; flex: 2;"><a href="10-testing-state-machines.html">Next: Testing State Machines →</a></div>
</div>