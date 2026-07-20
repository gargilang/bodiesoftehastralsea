# Design: First Prototype Slice — Boat Steering on Water

Date: 2026-07-21
Status: Approved (owner delegated remaining detail decisions to the agent)
Related concept: `docs/concept/09-narrative-development-and-prototype.md` (Recommended Prototype), `docs/concept/02-sailing-knowledge-and-physics.md`
Related decision: `docs/development/decisions/001-godot-csharp-android.md` (Accepted — C# on godot-mono for Android)

## Purpose

This is the first buildable slice of the game. It exists to answer one question from the concept's recommended prototype:

> Is the steering and camera foundation comfortable and controllable enough to build sailing on top of?

Everything else in the concept (wind, sails, tacking, currents, islands, story, tsunami) sits on top of boat movement and a comfortable third-person camera. We prove that foundation first, with placeholder art, before investing in sailing mechanics or Blender assets.

The concept's larger question — "Is sailing enjoyable for ten minutes?" — is explicitly a later slice. This slice proves the substrate: does the boat move and turn in a way that feels good, and is the camera comfortable for motion-sensitive players?

## Scope

### In scope

- A single playable Godot scene: a placeholder box-boat on a flat sea plane.
- A plain C# simulation core (`BoatMotion`) with no Godot dependency, holding position, heading, forward speed, and turning behaviour, advanced by a `Step(input, dt)` method.
- A thin Godot boat node (`Boat.cs`, `Node3D`) that owns a `BoatMotion`, feeds it input each physics frame, and applies the resulting state to its transform.
- Momentum-preserving movement: turning changes heading gradually; releasing controls does not instantly stop the boat; the boat coasts and decelerates via drag.
- Shared input: one input-reading layer that maps BOTH keyboard (desktop) and a touch-drag gesture (phone) to the same abstract control values (throttle intent and steering intent).
- A stable elevated third-person chase camera that follows position smoothly and rotates with heading using damping/lag (no instant snapping).
- Readability feedback with no numeric HUD in the normal view: a wake/foam trail behind the boat and a slight visual heel (roll) into turns.
- A developer-only debug overlay showing speed and heading numbers, hidden by default, toggled by a key/gesture, never part of the shipped experience.
- Desktop run and a signed debug APK that runs on the Android test device.
- Unit tests for `BoatMotion`.

### Out of scope (explicitly deferred to later slices)

- Wind, sail deployment, trim, sail angle, no-go zone, tacking.
- Engine model, fuel, efficiency-loss zone.
- Waves, buoyancy, heave/pitch from water, water intake, flooding.
- Currents, tides, weather, fog, storms.
- Islands, land areas, the four-angle island camera, exploration.
- Story, dialogue, notes, progression, save/load.
- Sound design and haptics (heel and wake are visual only in this slice).
- Blender / BlenderMCP assets. All meshes are Godot built-in primitives.
- Gyroscope input.
- MCP-driven authoring (Beckett/BlenderMCP) — not used in this slice.

## Approved Decisions

These were chosen during brainstorming, then remaining details delegated to the agent:

1. First slice proves **boat moves and steers on water** (not the full sailing loop).
2. Sea is a **flat plane, no waves**.
3. Input supports **both touch and keyboard**, sharing one logic path.
4. Architecture uses a **plain C# sim core plus a thin Godot node**.
5. Camera is a **stable elevated chase camera with smoothing**.
6. Feedback is **visual wake + turn heel + a hideable dev debug overlay** (no numeric HUD in the normal view).
7. Assets are **Godot primitives only; no Blender yet**.

## Architecture

Three clearly separated responsibilities, matching AGENTS.md's rule to keep simulation, integration, and presentation distinct.

### 1. Simulation core (plain C#, no Godot types)

`BoatMotion` — a plain class in a `Celestial.Sim` namespace, independent of the Godot scene tree and, where practical, of Godot value types. To keep it testable without an engine, it uses simple primitives (its own small 2D vector/struct or `double` fields for x, z, heading) rather than `Godot.Vector3`.

State:
- planar position (x, z on the water plane)
- heading (radians)
- forward speed (units/second)

Input to `Step`:
- `throttleIntent` in [-1, 1] (forward/back drive)
- `steerIntent` in [-1, 1] (turn left/right)
- `dt` (seconds)

Behaviour (tunable constants, named, no magic numbers):
- throttle accelerates/decelerates forward speed toward a max, with acceleration and drag constants.
- steering changes an angular velocity that is scaled by current speed (a near-stationary boat turns weakly — momentum matters), then heading integrates from angular velocity.
- drag continuously bleeds speed and angular velocity so releasing input coasts to rest rather than stopping dead.
- position integrates from speed along heading.

`BoatMotion` exposes read-only state and a small tuning struct (`BoatTuning`) so values can be adjusted from the Godot side and from tests.

### 2. Godot integration (thin)

`Boat.cs` (`Node3D`):
- owns a `BoatMotion` and a `BoatTuning`.
- in `_PhysicsProcess(dt)`: reads abstract input from the input layer, calls `boatMotion.Step(...)`, then writes the sim state onto its own `Position` (map sim x,z to world X,Z at fixed water height) and `Rotation.Y` (heading). Applies a presentation-only heel roll proportional to turn rate (does not feed back into the sim).
- holds references to the wake emitter and debug overlay and updates them from sim state.

`BoatInput` — a small class/autoload-free helper that produces `(throttleIntent, steerIntent)` from Godot input each frame:
- keyboard via Godot input actions (e.g. `boat_forward`, `boat_back`, `boat_left`, `boat_right`).
- touch: a drag on the screen produces steering from horizontal offset and throttle from vertical offset relative to the touch start, using `InputEventScreenTouch` / `InputEventScreenDrag`.
- both map into the same [-1, 1] range; the sim never knows which device was used.

### 3. Presentation

- `Boat` visual: a stretched box (`BoxMesh`) or prism with a visibly distinct bow so heading is readable. Child of the `Boat` node.
- Sea: a large `PlaneMesh` with a plain material at y = 0.
- Wake: a `GPUParticles3D` (or `CPUParticles3D` if needed for Android reliability) trailing the stern, emission rate tied to speed.
- Camera: `ChaseCamera.cs` (`Camera3D` or a `Node3D` rig) that each frame eases its position toward an offset behind/above the boat and eases its look-direction toward the boat, using separate position- and rotation-smoothing constants so turn comfort can be tuned.
- Debug overlay: a `CanvasLayer` + `Label` showing speed and heading, `Visible = false` by default, toggled by an action (`toggle_debug`) and, on touch, a long-press or a corner tap.

### Scene layout

```
Main (Node3D)
├── Sea (MeshInstance3D, PlaneMesh)
├── Boat (Node3D, Boat.cs)
│   ├── Hull (MeshInstance3D, BoxMesh, bow marked)
│   └── Wake (GPUParticles3D / CPUParticles3D)
├── ChaseCamera (Camera3D, ChaseCamera.cs)
├── SunLight (DirectionalLight3D)
└── DebugLayer (CanvasLayer)
    └── DebugLabel (Label)
```

## Data Flow

Per physics frame:

1. `BoatInput` reads Godot keyboard/touch → `(throttleIntent, steerIntent)` in [-1, 1].
2. `Boat._PhysicsProcess` passes those + `dt` to `BoatMotion.Step`.
3. `BoatMotion` updates speed, angular velocity, heading, position (pure math).
4. `Boat` reads sim state → sets its `Position`/`Rotation.Y`, applies presentation heel, updates wake rate, updates debug label.
5. `ChaseCamera._Process` eases toward the boat's new transform.

Authoritative motion is computed once in the sim core; visuals, camera, wake, and debug all interpret it. Nothing in presentation feeds back into the sim.

## Error Handling and Robustness

- `BoatMotion.Step` clamps `throttleIntent`/`steerIntent` to [-1, 1] and treats non-positive `dt` as a no-op, so a bad frame cannot corrupt state.
- Tuning constants are validated to be positive where required; defaults are sane and defined in one place.
- Touch handling ignores multi-finger input beyond the first active touch in this slice (multitouch is out of scope) and resets cleanly when the touch ends.
- If particles prove unreliable on the device, the wake is allowed to degrade to a simpler trail without affecting movement; movement never depends on presentation.

## Testing Strategy

- **Unit tests (primary, TDD):** `BoatMotion` tested with a standalone test project (xUnit or NUnit referencing the sim as plain C#), covering:
  - forward throttle increases speed toward max and no further.
  - zero input causes coasting: speed decays toward zero, never negative from drag alone.
  - steering at speed changes heading; steering at ~zero speed barely changes heading (momentum rule).
  - position advances along heading consistent with speed and dt.
  - input clamping and `dt <= 0` no-op.
- **Manual desktop validation:** run the scene, confirm steering feels momentum-preserving, camera eases without snapping, wake and heel read correctly, debug toggle works.
- **On-device validation:** export debug APK (per the confirmed pipeline in `environment.md`), install on the Vivo device, confirm touch-drag steering works, camera is comfortable, and frame pacing is acceptable.

The sim core is where correctness is asserted automatically; feel is judged manually on desktop and device.

## Repository and Toolchain Notes

- This slice creates the first Godot project at the repository root (no `project.godot` exists yet). Project/assembly name and layout will be set by the implementation plan following the Accepted C# Android requirements: `.csproj` targets `net9.0`, a classic `.sln` is present, ETC2/ASTC enabled, and the Android export preset targets arm64-v8a.
- The unit-test project must not break the Godot C# build; the plan will decide whether the sim lives in the Godot assembly (tested via a separate test project referencing the source) or in a shared class library, choosing the simplest option that keeps Godot export working.
- No new runtime dependencies. Test framework is a development-only dependency and must not ship in the APK.

## Success Criteria (definition of done)

1. `BoatMotion` unit tests pass.
2. The scene runs on desktop; steering feels controllable and momentum-preserving; camera eases comfortably through turns.
3. The same input logic drives keyboard and touch.
4. Wake trail and turn heel are visible; no numeric HUD in the normal view; debug overlay toggles on/off.
5. A signed debug APK installs and runs on the Android device, controllable by touch, with acceptable frame pacing.
6. Simulation, integration, and presentation remain separated per AGENTS.md; no presentation code contains authoritative movement rules.
