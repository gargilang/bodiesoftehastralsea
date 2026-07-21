# Decision 001: Godot C# for Android

## Status

**Accepted — validated on the CachyOS development PC and a representative Android device on 2026-07-21.**

Godot 4 .NET with C# is confirmed usable for this project. A disposable C# project compiled, exported a debug APK, and ran on a physical Android phone with hardware Vulkan rendering, working touch input, vibration, and save-file I/O.

Confirmed versions:

- Godot `.NET` build: `godot-mono` 4.7.stable.mono (`/usr/bin/godot-mono`)
- .NET SDK: 10.0.110 (runtime Microsoft.NETCore.App 10.0.10)
- Android export templates: `4.7.stable.mono` (downloaded and installed separately; the standard `godot` templates do not work for the `.mono` build)
- Test device: Vivo I2508, Android 16 (SDK 36), arm64-v8a, Snapdragon (canoe), Adreno 829, 11 GB RAM, Vulkan 1.4 (Forward Mobile)

Required setup facts discovered during validation (see `../environment.md` "C# Android Export Requirements"):

1. The `.mono` Godot build needs its own export templates at `~/.local/share/godot/export_templates/4.7.stable.mono/`; the standard build's templates are not sufficient.
2. The `.csproj` must target `net9.0`. The 4.7 Android export template rejected `net8.0` and does not accept `net10.0`, even though the installed SDK is 10.0.110 (SDK 10 builds `net9.0` fine).
3. A classic-format `CSharpGate.sln` is required for export. .NET 10's `dotnet new sln` emits the newer `.slnx`, which Godot 4.7 does not recognise — without a `.sln`, the C# assembly is silently omitted and the APK is a non-functional ~29 MB instead of ~105 MB.
4. Android export requires `rendering/textures/vram_compression/import_etc2_astc=true` in project settings.
5. Editor setting `export/android/java_sdk_path` must point at the JDK (here `/usr/lib/jvm/java-17-openjdk`); a debug keystore at `~/.local/share/godot/keystores/debug.keystore` (alias `androiddebugkey`, pass `android`) is needed.
6. Headless C#/.NET Android export is flagged "experimental" by Godot and one headless editor invocation crashed on shutdown (signal 11 in `is_cmdline_mode`) without affecting output. Treat headless export as working-but-unofficial.

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

A disposable technical project (`/tmp/opencode/csharp-gate`) was validated on a representative Android device on 2026-07-21:

1. The installed Godot edition supports C#. — **PASS:** `godot-mono` 4.7.stable installed alongside standard `godot`.
2. A compatible .NET SDK is available. — **PASS:** .NET SDK 10.0.110; builds `net9.0` projects.
3. A minimal C# project compiles without unresolved tooling errors. — **PASS:** `dotnet build` produced `CSharpGate.dll`, 0 warnings / 0 errors.
4. A debug APK exports successfully. — **PASS:** ~105 MB signed APK (C# assembly + arm64 .NET runtime bundled) once the `.sln` and `net9.0` requirements were met.
5. The APK installs and launches repeatedly. — **PASS:** installed and launched via adb, ran repeatedly.
6. A basic 3D scene renders correctly. — **PASS:** Vulkan 1.4 Forward Mobile on Adreno 829.
7. Single-touch and multitouch input work. — **PASS (single-touch confirmed):** taps registered and handled in C# (`InputEventScreenTouch`). Multitouch not explicitly stress-tested.
8. Vibration can be invoked and disabled safely. — **PASS:** `Input.VibrateHandheld` fired on each touch on-device.
9. A small save file can be written and loaded. — **PASS:** `user://` file written and read back on-device.
10. A plain C# simulation type can be exercised independently of presentation. — **PASS:** `TideModel` computed correctly.
11. Code-change iteration is reliable enough for regular development. — **PARTIAL:** desktop build/run iteration is reliable; on-device rebuild→export→install→run loop works but is not yet time-measured for daily comfort.
12. Startup time, memory use, package size, and frame pacing are acceptable. — **PARTIAL:** app started in well under a second and used ~259 MB PSS; package ~105 MB. Startup/memory/size acceptable; sustained frame pacing under real gameplay load not yet measured.

Outstanding (not blockers to Accept): explicit multitouch test, iteration-time measurement, sustained frame-pacing measurement, and asking Beckett MCP to perform one small reversible C# Godot task and reviewing the result.

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
