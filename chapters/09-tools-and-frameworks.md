---
title: Best State Machine Tools and Frameworks – Libraries and Modeling Software
date: 2025-09-22
last_modified_at: 2025-09-22
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
- Validate workflow: visual debugging, testing/coverage, traceability, documentation.
- Assess interoperability: SCXML, integration with existing code/build, VCS diff/merge.
- Ensure CI readiness: headless execution, license model for build agents.
- Weigh learning curve and total cost (including required add-ons).

---
## Modeling and Code Generation Tools

This section lists representative options rather than a complete catalog; use it as a starting point and verify current capabilities for your needs.

- [itemis CREATE](https://www.itemis.com/en/products/itemis-create/): Graphical statechart modeling focused on executable specifications. Includes simulation/visual debugging, validation, unit testing, coverage, and code generation (C, C++, Java, Python, SCXML). Offers a Desktop app, Web app, and VS Code extension. Supports SCXML and deep integration with handwritten C/C++ or Java code. Provides a headless CLI for CI and an embeddable interactive player for documentation/reviews.
- [Stateflow](https://www.mathworks.com/products/stateflow.html) (MathWorks): Statecharts integrated into MATLAB/Simulink workflows. Strong for signal/control engineering with powerful simulation when used with the broader Simulink stack. Code generation typically via additional toolboxes (e.g., Simulink/Embedded Coder).
- [IBM Rhapsody](https://www.ibm.com/products/rhapsody): UML/SysML environment with state machines among many diagram types. Suited to specification-driven, safety-critical contexts. Broad modeling scope; setup and usage can be more involved.


### Feature Matrix (Modeling & Generation Tools)

A quick, high-level comparison to help shortlist tools; verify versions and features for your environment.

| Feature | [itemis CREATE](https://www.itemis.com/en/products/itemis-create/) | [Stateflow](https://www.mathworks.com/products/stateflow.html) (MathWorks) | [IBM Rhapsody](https://www.ibm.com/products/rhapsody) |
|---|---|---|---|
| Platforms | Desktop, Web App, VS Code extension | Desktop/Web via MATLAB/Simulink | Desktop |
| Modeling focus | Purpose-built statecharts | Statecharts within MATLAB/Simulink | UML/SysML incl. state machines |
| Simulation & visual debugging | Built-in, interactive | Powerful within MATLAB/Simulink stack | Available; setup can be complex |
| Code generation | C, C++, Java, Python, SCXML | C/C++ via Simulink/Embedded Coder add-ons | C (optionally C++), tool-specific patterns |
| SCXML support | Yes | No | No |
| Integration with handwritten code | Deep integration (C/C++/Java), side-by-side | External C calls; depends on Simulink env | External C calls; tighter tool coupling |
| Validation, testing, coverage | Live validation; unit testing and coverage in Desktop | Design Verifier/Coverage via add-ons | Model checks, test integrations |
| Collaboration / VCS | Git/SVN integration; model diff/merge | Simulink Projects | Centralized multi-user infrastructure |
| CI/headless automation | CLI for headless codegen | Possible; requires MATLAB licenses/agents | CLI integration with scripts |
| Learning curve | Generally approachable for dev teams | Natural for control engineers | Higher due to UML/SysML breadth |
| Licensing/add-ons | Single license for full feature set (per materials) | Modular toolboxes for many workflows | Enterprise licensing, feature-dependent |

Note: Details are summarized from the provided material and vendor documentation. Always verify current capabilities and licensing for your specific version.

---

## Statemachine Frameworks (by Ecosystem)

### Java / JVM
- [Spring Statemachine](https://spring.io/projects/spring-statemachine): Mature framework with hierarchical states, regions, guards, actions, events, and persistence.
- [Apache Commons SCXML](https://commons.apache.org/proper/commons-scxml/): SCXML interpreter for the JVM.

### C++
- [Boost MSM](https://www.boost.org/doc/libs/release/libs/msm/doc/HTML/index.html) / [Boost.SML](https://boost-ext.github.io/sml/): Compile-time state machine libraries with strong type-safety and performance.
- [Qt State Machine Framework](https://doc.qt.io/qt-6/qtstatemachine-index.html): Part of Qt; integrates with signals/slots and QML.

### JavaScript / TypeScript
- [XState](https://xstate.js.org/): Full-featured statecharts with visualizer, actors, and tooling.

### C# / .NET
- [Stateless](https://github.com/dotnet-state-machine/stateless): Lightweight C# state machine library with a fluent API.

### Python
- [Sismic](https://sismic.readthedocs.io/) and [transitions](https://github.com/pytransitions/transitions): Declarative state machine libraries with readable APIs.

### Embedded C/C++
- [RKH](https://github.com/vortexmakes/RKH): Lightweight frameworks with hierarchical states and event-driven kernels.

### SCXML Engines (cross-platform)

SCXML is a W3C standard for state charts. Engines exist across platforms (e.g., [Apache Commons SCXML](https://commons.apache.org/proper/commons-scxml/) for Java, [SCION](https://github.com/jbeard4/SCION)) for Javascript/Web. Choose SCXML when you need model interchange or runtime-loaded machines.

---

## Summary

The right tool depends on your context: platform and language needs, required statechart features, workflow and collaboration tooling, and operational fit for CI and licensing. Use the quick checklist to shortlist candidates, then prototype a core flow to validate ergonomics and integration. When in doubt, favor clarity, testability, and interoperability.

---

{%- include faq.html -%}

---

<div style="display: flex; justify-content: space-between; width: 100%; align-items: center;">
  <div style="text-align: left; flex: 2;"><a href="08-implementation-strategies.html">← Previous: Implementation Strategies</a></div>
  <div style="text-align: center; flex: 1;"><a href="#top">Back to top</a></div>
  <div style="text-align: right; flex: 2;"><a href="10-summary-and-next-steps.html">Next: Summary and Next Steps →</a></div>
</div>