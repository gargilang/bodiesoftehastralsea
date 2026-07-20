# Decision 001: Godot C# for Android

## Status

**Provisional — requires validation on the CachyOS development PC and representative Android hardware.**

## Context

Celestial is expected to be developed over one to two years by a solo owner with extensive AI-agent assistance. Its systems will include boat movement, wind and current fields, sail alignment, engine and fuel behavior, weather, equipment, fishing, island-specific myths, tsunami triggers, narrative knowledge, charts, and expedition persistence.

The owner intends to target Android only. Godot and Blender are reportedly installed on the development PC, while the exact Godot edition, .NET availability, and Android toolchain status remain unknown.

## Provisional Decision

Use Godot 4 .NET with C# as the primary and normally exclusive gameplay language, provided the validation gate below succeeds.

Do not mix C# and GDScript by default. A required addon may contain GDScript, but project-authored mixed-language architecture requires a separate explicit decision.

## Rationale

C# is provisionally preferred for:

- static typing across interconnected systems
- safer long-term refactoring
- explicit interfaces and data structures
- testable simulation code with limited scene-tree coupling
- compile-time detection of many agent-generated mistakes
- consistent conventions over a long agent-assisted project
- the owner's expectation of substantial systemic complexity

GDScript remains a valid fallback because it offers tighter native integration, fewer external toolchain requirements, fast iteration, and broad Godot-specific community examples.

Complexity alone does not make C# architecture good. Clear system boundaries, data ownership, and validation remain mandatory.

## Android Validation Gate

Before production implementation, create a disposable technical project and confirm on a representative Android device:

1. The installed Godot edition supports C#.
2. A compatible .NET SDK is available.
3. A minimal C# project compiles without unresolved tooling errors.
4. A debug APK exports successfully.
5. The APK installs and launches repeatedly.
6. A basic 3D scene renders correctly.
7. Single-touch and multitouch input work.
8. Vibration can be invoked and disabled safely.
9. A small save file can be written and loaded.
10. A plain C# simulation type can be exercised independently of presentation.
11. Code-change iteration is reliable enough for regular development.
12. Startup time, memory use, package size, and frame pacing are acceptable for the representative device.

Also ask Beckett MCP to perform one small, reversible C# Godot task. Review whether it creates idiomatic, compilable code and valid scene integration without exceeding its approved permissions.

## Success Outcome

If the validation gate succeeds, change this decision to **Accepted**, record the confirmed Godot and .NET versions, and establish C# and Godot coding conventions.

## Failure Outcome

Do not switch languages after a single unexplained error. First isolate whether the problem is:

- missing configuration
- incompatible versions
- an incomplete Android toolchain
- a Beckett MCP limitation rather than a Godot limitation
- device-specific behavior
- an actual unacceptable C# workflow or runtime problem

Time-box the investigation. If C# Android development remains unreliable or unsuitable, change this decision to **Rejected** and adopt typed GDScript before production gameplay begins.

## Consequences

If accepted:

- the .NET-enabled Godot build and a compatible .NET SDK become required development tools
- Android validation remains the authority; successful desktop execution is insufficient
- agents must follow Godot-specific C# lifecycle and performance practices
- frame-critical code must avoid unnecessary allocations and scene-tree searches
- simulation logic should be separated from input and presentation where practical

If rejected:

- document the evidence and selected GDScript conventions
- do not retain an accidental mixed-language prototype as the production foundation
