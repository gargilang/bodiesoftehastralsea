# Sprite Pipeline Standard (OPTIONAL — NOT THE DEFAULT PATH)

> **Status: Optional future optimization. This is NOT the primary pipeline.**
>
> The **Provisional decision** for *Bodies of the Astral Sea* is **real-time low-poly 3D** for both characters and the world, rendered under a fixed isometric orthographic camera (`Standards/Cameras.md`, `docs/concept/08-art-and-immersion.md`). Do not bake assets to 2D sprites unless a specific, approved optimization decision says to.

This document exists so the option is understood, not to authorize it.

## Why 3D Is the Default

- The camera uses four fixed 90° orientations. In real-time 3D these four facings are free — the model is built once and rotated live.
- For a whole world (houses, docks, rocks, vegetation, props, the boat), real-time 3D is cheaper to author and maintain than baking and re-baking sprite sheets for every animated asset and every angle.
- Reference games with this look (*Dredge*, *Spilled*) are real-time 3D under a controlled camera, not sprite-based.

## When Sprite Baking Might Be Considered (future, requires approval)

Only as a targeted optimization, never as the base pipeline, and only if on-device profiling shows a real problem that sprites would solve:

- Distant, numerous, low-importance objects where live skinned 3D is too expensive (for example background crowds or dense foliage instances).
- A specific asset whose painterly detail is worth pre-rendering and whose animation set is small and fixed.
- A memory or draw-call ceiling that real-time 3D provably cannot meet on target hardware.

## If It Is Ever Approved

The baking process would reuse the existing 3D asset (no separate hand-drawn art):

1. Build, rig, animate, and texture the asset in 3D exactly as documented for the 3D pipeline.
2. Set up the isometric orthographic render camera matched to the game camera (`Standards/Cameras.md`).
3. Render each required animation across each of the four fixed orientations into sprite sheets / texture atlases.
4. Pack into Godot as `AnimatedSprite3D`/`Sprite3D` or a 2D sprite setup, per an approved integration decision.
5. Establish a **re-bake procedure**, because any change to the source model requires re-rendering all affected sheets — this maintenance cost is the main reason sprites are not the default.

## Do Not

- Do not bake Kaya (or any asset) to sprites as part of normal work.
- Do not treat `CHAR_Kaya_SpriteRender.md` as an active task; it is a stub pointing here.
- Do not introduce a sprite pipeline without an explicit owner/decision record, following `AGENTS.md`.
