# Kaya — Sprite Render (OPTIONAL STUB — NOT ACTIVE WORK)

> **Status: Not part of the current pipeline.**
>
> Kaya is a **real-time low-poly 3D** character (Provisional decision; see `CHAR_Kaya_Overview.md` and `docs/Art/Standards/SpritePipeline.md`). She is **not** baked to 2D sprites.

This document is a placeholder so the structure is complete. It does not authorize sprite work.

## When This Would Apply

Only if an explicit, approved optimization decision calls for baking Kaya (or specific assets) to 2D sprites — for example a proven on-device performance/memory problem that sprites solve. See the conditions in `docs/Art/Standards/SpritePipeline.md`.

## If Ever Activated

Follow `docs/Art/Standards/SpritePipeline.md`. In summary: reuse the approved 3D Kaya (mesh, rig, animations, textures), set up the isometric orthographic render camera matched to the game camera (`Standards/Cameras.md`), render each animation across the four fixed orientations into sprite sheets, integrate per an approved Godot decision, and establish a re-bake procedure for future source changes.

## Do Not

- Do not perform sprite baking as part of normal Kaya work.
- Do not treat this document as an active task.
- Do not introduce a sprite pipeline without an explicit decision record (`AGENTS.md`).
