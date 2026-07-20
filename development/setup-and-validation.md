# Setup and Validation Plan

## Status

This is a provisional plan. It contains no installation commands because exact versions and missing components have not been confirmed on the development PC.

## Principles

- Audit before installing.
- Preserve working configuration.
- Install only demonstrated missing requirements.
- Obtain approval before system-wide changes, network access, dependency additions, or configuration edits.
- Validate Android early and repeatedly.
- Record exact versions only after observing them.

## Stage 1: Read-Only Audit

Complete the checklist in `environment.md`. Classify each item as confirmed, missing, incompatible, or unknown.

Do not install .NET merely because C# is planned. First confirm the Godot version, Godot edition, currently installed SDKs, and official compatibility requirements for that exact release.

## Stage 2: Toolchain Proposal

If anything is missing, present a proposal containing:

- component name
- reason it is required
- exact compatible version or version range
- source and mirror availability
- expected disk and system impact
- whether administrator privileges are needed
- configuration files that would change
- rollback approach
- license considerations

Wait for approval before installation or reconfiguration.

## Stage 3: C# Android Validation

Run the validation gate in `decisions/001-godot-csharp-android.md` using a disposable project. Do not begin production systems until the decision is accepted or rejected.

## Stage 4: MCP Validation

### Beckett MCP

Use a small disposable Godot task to determine:

- supported Godot operations
- quality of generated C#
- scene and resource editing behavior
- filesystem boundaries
- command-execution capability
- network capability
- error reporting
- startup and shutdown behavior

### BlenderMCP

Use a disposable asset to determine:

- whether editable Blender source is preserved
- mesh, material, UV, and naming quality
- GLB export behavior
- filesystem boundaries
- command-execution and network capabilities
- startup and shutdown behavior

Do not let either test overwrite valuable source files.

## Stage 5: Asset Round Trip

Validate this minimal pipeline:

1. Create or generate a simple low-poly asset in Blender.
2. Save the editable `.blend` source.
3. Export a `.glb` using the intended coordinate, scale, and naming conventions.
4. Import it into Godot.
5. Add collision and a scene wrapper without modifying the generated source destructively.
6. Export and inspect it on Android.

## Stage 6: Prototype Authorization

After the toolchain and language decision are validated, establish:

- repository layout
- C# and Godot naming conventions
- target Android device and performance budget
- renderer choice
- source asset and generated export locations
- test strategy
- backup and version-control workflow

Only then begin the sailing prototype described in the concept documents.

## Validation Records

Every validation record should include:

- date
- machine role
- relevant versions
- device used
- exact capability tested
- observed result
- logs or screenshots when useful and safe
- limitations not tested
- resulting documentation or decision update

Never report a capability as validated merely because a tool is installed or a desktop test succeeded.
