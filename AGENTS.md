# Agent Instructions

These instructions apply to the entire repository. A closer `AGENTS.md` may add or override rules for its own subtree.

## Project Context

Bodies of the Astra Sea is a long-term, solo-developed, Android-only maritime exploration game planned for Godot 4. Development will be assisted through OpenCode, Beckett MCP, and BlenderMCP.

Read `README.md` and the relevant documents under `docs/concept/` and `docs/development/` before changing implementation or settled design.

## Evidence and Assumptions

Every technical statement must be treated as one of:

- **Confirmed:** observed on the current development PC or proven by a test.
- **Owner decision:** explicitly chosen by the project owner.
- **Provisional decision:** preferred direction awaiting technical validation.
- **Assumption:** plausible but not yet verified.
- **Unknown:** information that must be discovered before proceeding.

Do not convert an assumption into a confirmed fact by repeating it. When validation changes an assumption, update the relevant document and record what was tested.

The current documentation was initially prepared on a restricted Windows planning PC. It does not prove that tools, versions, paths, exports, or integrations work on the CachyOS development PC.

## Environment Safety

- Inspect before installing, upgrading, repairing, or reconfiguring.
- Do not reinstall a tool merely because its installation differs from expectations.
- Do not use `sudo` or make system-wide changes without explicit approval.
- Do not modify package mirrors, repositories, proxies, Fish, Kitty, OpenCode, or MCP configuration without explicit approval.
- Do not start a server, listener, or externally reachable service without explicit approval.
- Do not expose a local service beyond loopback without explicit approval.
- Do not upload source code, documents, prompts, logs, or assets without explicit approval.
- Never place credentials, tokens, or private keys in the repository.
- Explain the purpose and impact of any command that changes the machine before running it.
- Prefer reversible and project-local changes.
- Preserve user-created work and inspect files before overwriting them.

## Dependency Discipline

- Do not add or upgrade a dependency without explicit approval.
- First check whether Godot, .NET, Blender, an existing addon, or repository code already provides the capability.
- For every proposed dependency, document its purpose, exact source, version requirement, license, Android compatibility, maintenance risk, and removal cost.
- Respect the machine's existing mirror and network configuration.
- Do not assume that a public package host is reachable.
- Keep optional development tools from becoming runtime requirements.

## Language and Platform Direction

- Android is the only planned product platform. Desktop execution may support development but is not proof of Android compatibility.
- Godot 4 .NET and C# are the provisional primary engine and language choice.
- Do not begin production implementation until the C# Android validation gate in `docs/development/decisions/001-godot-csharp-android.md` has been evaluated on the development PC.
- Do not introduce GDScript by default. A language change or mixed-language architecture requires an explicit decision update.
- Mobile performance is an architectural constraint from the beginning.

## Code Quality

- Prefer clear, explicit, maintainable code over clever or compressed code.
- Use descriptive names and the established naming conventions.
- Keep functions focused on one responsibility.
- Avoid unexplained magic numbers; expose gameplay tuning through named configuration or data resources.
- Separate simulation, Godot integration, presentation, persistence, and input responsibilities.
- Compute authoritative gameplay state once; let visuals, audio, animation, UI, and haptics interpret it.
- Do not place core gameplay rules in UI, shaders, audio, or haptic code.
- Avoid circular dependencies and unrestricted global state.
- Follow existing patterns before introducing abstractions.
- Add comments for non-obvious intent, constraints, or tradeoffs—not to restate syntax.
- Do not present placeholders, generated assets, or unvalidated behavior as finished work.

Exact C# and Godot naming conventions will be documented after the installed toolchain is validated.

## Game-Specific Principles

- Favor stable, learnable, simplified systems over expensive physical realism.
- The sea simulation may be intentionally faked, but its outcomes and feedback must remain consistent.
- Knowledge progression must remain more important than statistical progression.
- Crafting and upgrades must remain optional for completion unless an approved design decision says otherwise.
- Important forces should use multiple feedback channels where practical; haptics and gyroscope input must have accessible alternatives.
- Do not silently change an established concept. Report conflicts between implementation constraints and concept documents.

## MCP and Generated Work

- Treat MCP-generated code and assets as unreviewed until inspected and validated.
- Use Beckett MCP only for approved Godot-development tasks and BlenderMCP only for approved Blender asset tasks until their exact capabilities are documented.
- Do not allow an MCP tool to install software, alter global configuration, access new network services, or overwrite hand-authored source assets without explicit approval.
- Keep editable source assets, export artifacts, prompts, and generation parameters traceable.
- Validate generated Godot scenes, code, Blender files, and exports before accepting them.

## Workflow

Before work:

1. Read `README.md`, this file, relevant concept documents, applicable decisions, and the current handoff.
2. Confirm whether each required environment capability is confirmed, provisional, assumed, or unknown.
3. Inspect existing implementation before proposing changes.
4. Identify the smallest complete change and its validation method.

After work:

1. Run the narrowest relevant validation that the environment permits.
2. State exactly what was and was not validated.
3. Update permanent documentation when a confirmed fact or settled decision changes.
4. Create or update a handoff at a meaningful checkpoint, when work is paused incomplete, or when another agent needs non-obvious context.
5. Do not commit, push, publish, or release unless explicitly requested.

## Handoffs

Use `docs/handoffs/template.md`. A handoff is temporary operational context, not the permanent source of truth. Move permanent decisions into `docs/development/decisions/` and permanent design into `docs/concept/`.
