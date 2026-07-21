# Game Concept

A knowledge-driven maritime exploration game about a fisherman searching for their missing archaeologist parents. The player leaves familiar engine-powered waters for a supernatural sea where observation, traditional sailing, preparation, and understanding are more effective than brute technological force.

Across ordinary and mystical islands, the player learns wind, currents, stars, sound, tides, and island-specific laws. A recurring nonlethal tsunami repels intruders, structures each expedition day, and can eventually be exploited as a high-skill route. The personal search gradually reveals Atlantis, the truth of the parents’ choices, and a mystery larger than rescue or treasure.

## Concept Documents

1. [Vision, Premise, and Opening](docs/concept/01-vision-premise-and-opening.md) — high-level vision, core premise, opening fishing trip, world structure, and the transition from engine to sail.
2. [Sailing Knowledge and Physics](docs/concept/02-sailing-knowledge-and-physics.md) — knowledge progression, hidden controls, sailing inputs and feedback, learning sequence, navigational puzzles, and systemic conditions.
3. [Ocean, Tsunami, and Time](docs/concept/03-ocean-tsunami-and-time.md) — the nonlethal expedition loop, tsunami mastery, warning languages, and time-dependent world changes.
4. [Mystical Islands and Lore](docs/concept/04-mystical-islands-and-lore.md) — the eight island concepts, Atlantis, and the parents’ conflicting mystery.
5. [Boat, Progression, and Economy](docs/concept/05-boat-progression-and-economy.md) — the persistent family vessel, horizontal equipment, optional skills, fishing, and the non-grinding economy.
6. [Preparation and Charting](docs/concept/06-preparation-and-charting.md) — pre-voyage strategy, imperfect forecasts, physical charts, persistent annotations, and navigation without GPS.
7. [Camera, Mobile Controls, and Accessibility](docs/concept/07-camera-mobile-and-accessibility.md) — stable perspectives, diegetic gestures, gyroscope options, haptics, and equivalent accessible controls.
8. [Art and Immersion](docs/concept/08-art-and-immersion.md) — painterly low-poly visual direction, the Godot/Android asset pipeline, reusable asset guidance, and low-HUD immersion.
9. [Narrative, Development, and Prototype](docs/concept/09-narrative-development-and-prototype.md) — narrative phases, design philosophy, project scope, prototype boundaries, validation questions, and final identity.

## Development Bootstrap

The development documentation is provisional until validated on the CachyOS development PC.

1. [Agent Instructions](AGENTS.md) — repository-wide safety, quality, dependency, platform, and workflow rules.
2. [Development Environment](docs/development/environment.md) — owner-provided facts, assumptions, unknowns, network constraints, and the read-only audit checklist.
3. [Godot C# for Android Decision](docs/development/decisions/001-godot-csharp-android.md) — provisional language direction and the mandatory Android validation gate.
4. [Setup and Validation Plan](docs/development/setup-and-validation.md) — inspection-first bootstrap stages without unverified installation commands.
5. [Agent Workflow](docs/agents/workflow.md) — task lifecycle, approval boundaries, source-of-truth order, and checkpoint guidance.
6. [Handoff Template](docs/handoffs/template.md) — consistent context transfer for milestones, pauses, blockers, and agent changes.

## Current Planning Status

- **Confirmed owner direction:** Android-only game, developed primarily through OpenCode with Beckett MCP and BlenderMCP.
- **Confirmed environment (audited 2026-07-21):** CachyOS (kernel 7.0.12, Wayland/KDE, i5-12450H, 15 GiB RAM, Intel UHD + RTX 3050). Godot 4.7.stable in both standard and `.mono` editions, Blender 5.1.2, OpenCode 1.18.4. Beckett MCP and BlenderMCP configured in OpenCode. See [Development Environment](docs/development/environment.md) for the full audit.
- **Accepted technical direction:** Godot 4 .NET with C# as the primary language. Validated end to end on 2026-07-21 — a disposable C# project compiled, exported a signed APK, and ran on a physical Android phone with hardware Vulkan rendering, touch input, vibration, and save I/O. See [Decision 001](docs/development/decisions/001-godot-csharp-android.md).
- **Confirmed toolchain:** `godot-mono` 4.7.stable.mono + .NET SDK 10.0.110; Android export requires `net9.0`, a classic `.sln`, the `.mono` export templates, and ETC2/ASTC — details in [environment.md](docs/development/environment.md).
- **Recommended next actions:** establish repository layout and C#/Godot conventions, validate Beckett MCP and BlenderMCP, and validate the Blender `.blend` → `.glb` → Godot asset round trip. Still to measure: multitouch, iteration time, and sustained on-device frame pacing under real load.
- **Not yet confirmed:** renderer choice for production, MCP capabilities and permissions, and the Blender-to-Godot asset round trip.

## Original Top-Level Section Accounting

Every original H1 section is represented once in the category documents:

| Original H1 section | Category |
| --- | --- |
| Game Concept Summary | [01](docs/concept/01-vision-premise-and-opening.md) |
| Core Premise | [01](docs/concept/01-vision-premise-and-opening.md) |
| The Opening Sequence | [01](docs/concept/01-vision-premise-and-opening.md) |
| The World Structure | [01](docs/concept/01-vision-premise-and-opening.md) |
| Transition from Engine to Sail | [01](docs/concept/01-vision-premise-and-opening.md) |
| Knowledge-Based Progression | [02](docs/concept/02-sailing-knowledge-and-physics.md) |
| Hidden but Available Controls | [02](docs/concept/02-sailing-knowledge-and-physics.md) |
| Camera and Player Perspective | [07](docs/concept/07-camera-mobile-and-accessibility.md) |
| Sailing as the Core Gameplay | [02](docs/concept/02-sailing-knowledge-and-physics.md) |
| Sailing Knowledge Sequence | [02](docs/concept/02-sailing-knowledge-and-physics.md) |
| Sailing as a Puzzle | [02](docs/concept/02-sailing-knowledge-and-physics.md) |
| Simulated Physics and Systemic Conditions | [02](docs/concept/02-sailing-knowledge-and-physics.md) |
| The Tsunami Expedition Loop | [03](docs/concept/03-ocean-tsunami-and-time.md) |
| Time-Dependent World Changes | [03](docs/concept/03-ocean-tsunami-and-time.md) |
| The Eight Mystical Islands | [04](docs/concept/04-mystical-islands-and-lore.md) |
| Atlantis | [04](docs/concept/04-mystical-islands-and-lore.md) |
| The Parents’ Mystery | [04](docs/concept/04-mystical-islands-and-lore.md) |
| The Boat as a Character | [05](docs/concept/05-boat-progression-and-economy.md) |
| Upgrades and Crafting | [05](docs/concept/05-boat-progression-and-economy.md) |
| Fishing, Rumors, and a Non-Grinding Economy | [05](docs/concept/05-boat-progression-and-economy.md) |
| Optional Skill Progression | [05](docs/concept/05-boat-progression-and-economy.md) |
| Strategic Preparation | [06](docs/concept/06-preparation-and-charting.md) |
| Charting | [06](docs/concept/06-preparation-and-charting.md) |
| Gesture-Based Mobile Controls | [07](docs/concept/07-camera-mobile-and-accessibility.md) |
| Gyroscope and Accessibility | [07](docs/concept/07-camera-mobile-and-accessibility.md) |
| Visual Direction and 3D Art Pipeline | [08](docs/concept/08-art-and-immersion.md) |
| Immersion Principles | [08](docs/concept/08-art-and-immersion.md) |
| Narrative Structure | [09](docs/concept/09-narrative-development-and-prototype.md) |
| Main Design Philosophy | [09](docs/concept/09-narrative-development-and-prototype.md) |
| Development Scope | [09](docs/concept/09-narrative-development-and-prototype.md) |
| Recommended Prototype | [09](docs/concept/09-narrative-development-and-prototype.md) |
| Final Identity of the Game | [09](docs/concept/09-narrative-development-and-prototype.md) |
