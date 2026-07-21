# Blender Rules Standard

Reference specification for how assets are built in Blender so they are clean, game-ready, and safe to produce through BlenderMCP. Source: `AGENTS.md`, `docs/concept/08-art-and-immersion.md`, `docs/development/environment.md`.

This document has precedence over asset documents on matters of topology, transforms, and safety.

## Environment (Confirmed)

- **Blender 5.1.2**, at `/usr/local/bin/blender` (manual install, not pacman-managed).
- Produced through **BlenderMCP** (local MCP) under agent assistance, per `AGENTS.md`.
- Target engine: **Godot 4.7 (`godot-mono`)**; import format **glTF/GLB**.

## Safety Rules (from AGENTS.md — non-negotiable)

- Treat MCP-generated geometry and assets as **unreviewed until inspected and validated**.
- Do **not** let BlenderMCP install software, change global configuration, access new network services, or overwrite hand-authored source assets without explicit approval.
- Do **not** overwrite an existing `.blend` or source asset without inspecting it first and confirming intent.
- Keep editable source, export artefacts, and any generation parameters **traceable**.
- Validate generated scenes and exports before accepting them.
- Do not present generated or unvalidated geometry as finished work.

## Scene Setup

- Units: Metric, unit scale 1.0, 1 unit = 1 metre (see `Standards/Scale.md`).
- Z-up authoring; forward-facing convention per `Standards/Scale.md`.
- Keep the scene organized in collections per `Standards/Naming.md`.
- Keep references and the check camera in a `_Ref` collection that is never exported.

## Topology

- **Low-poly with a strong silhouette.** Geometry provides silhouette and large form only; detail is painted (`Standards/Materials.md`).
- **Clean, mostly quad topology.** Quads for anything that deforms; limited tris allowed on rigid, non-deforming parts. Avoid n-gons on anything that will deform or that the camera sees closely.
- **Edge loops where deformation happens** (elbows, knees, shoulders, hips) for characters — enough to bend, no more.
- **No wasted geometry.** No dense subdivisions, no interior faces the camera never sees, no doubled vertices.
- **No high-frequency detail.** No sculpted grain, no tiny bevels that vanish at mobile scale.
- Remove hidden/occluded geometry that is never seen from the isometric camera (`Standards/Cameras.md`).

## Pivots (Origins)

- Set object origin deliberately; do not leave it at an arbitrary location.
- **Characters:** origin at the **feet, centred on the ground plane** (between the feet, on the floor), so the character plants correctly on the tile grid.
- **Environment/props:** origin at the logical placement point — usually the base centre so the piece sits on a tile.
- **Interactive/moving parts:** origin at the hinge/axis of motion (documented per asset).

## Transforms

- **Apply all transforms** (location, rotation, scale) before export. Scale must be a clean `1,1,1`; rotation clean; the mesh must not rely on object-level transforms to look correct.
- No negative scale (it flips normals). Mirror by applying, not by leaving negative scale.
- Recalculate normals outside; no flipped faces.

## Modularity and Separation

Separate an object into its own mesh **only** when it must be separate for a real reason:

- It animates or deforms independently.
- It is an interaction point.
- It needs a distinct material that cannot share the atlas.

Otherwise **keep meshes joined** to reduce object and draw-call count. Separation rules per asset live in that asset's Modeling document.

## Collision

- Provide **collision-friendly geometry** where the asset needs collision (per `docs/concept/08`). Collision shape strategy (primitive vs. simplified mesh) is defined per asset and confirmed against Godot's import options.
- Do not use the render mesh directly as collision unless it is already simple enough; prefer a simplified collision shape.

## Materials and UVs

- Follow `Standards/Materials.md` (few materials, shared atlas, hand-painted albedo, restrained roughness).
- UV unwrap efficiently; no wasted UV space, no overlapping UVs unless intentionally mirrored and documented.

## glTF / GLB Export

- Export format: **glTF Binary (.glb)** unless an asset document states otherwise.
- Apply transforms first (above).
- Include only what is needed: mesh, materials, and — at the relevant stage — armature and animations.
- Exclude the `_Ref` collection, the check camera, and any working-only objects.
- Naming per `Standards/Naming.md`.
- Detailed per-asset export settings and destination live in the asset's Export document.

> **Assumption:** The Blender→glTF→Godot round trip is not yet validated on this machine (`docs/development/environment.md`). No export setup here is Confirmed until the round trip is proven. Report round-trip problems; do not work around them silently.

## Working Method

- Work **incrementally**. Complete one step, then pause.
- **Save the `.blend`** after each major step.
- After each step: explain what changed, list any compromises, and **await approval** before continuing (see each asset's Agent Task Brief).
- Never continue automatically past a pause point.

## Validation Gate

The topology, transform, collision, and export rules here are best-practice defaults. The parts that depend on the Godot round trip (export settings, collision import, material channel mapping, scale) are **Provisional/Assumed** until the round trip is validated. Record confirmed settings back into this document once proven.
