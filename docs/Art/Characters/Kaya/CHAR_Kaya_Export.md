# Kaya — Export

Specification for exporting Kaya from Blender to **glTF/GLB** and importing into Godot. This is the stage where the Blender→glTF→Godot round trip is actually proven for the character.

## Prerequisite

Approved mesh, textures, rig, animations, and LOD decision. Export is the last stage.

## Objective

A clean `.glb` (or set of `.glb` files) that imports into Godot 4.7 (`godot-mono`) with correct scale, orientation, materials, skeleton, and animations, readable under the isometric camera on a physical Android device.

## Scope Fences

- Do **not** re-author mesh/rig/animation to work around export problems without approval — fix the source correctly or report the round-trip issue.
- Do **not** commit export settings as "Confirmed" until the round trip is actually validated on-device.

## Inputs (read first)

1. `AGENTS.md`
2. `docs/Art/Standards/BlenderRules.md` — transforms, glTF rules, safety.
3. `docs/Art/Standards/Scale.md` — 1 unit = 1 m, orientation, validation gate.
4. `docs/Art/Standards/Naming.md` — export file names.
5. `docs/Art/Standards/Materials.md` — material channel expectations, ETC2/ASTC.
6. `docs/development/environment.md` — confirmed toolchain and Android export requirements.
7. All prior Kaya stage documents.

## Confirmed Environment Facts (from `docs/development/environment.md`)

- **Blender 5.1.2**, **Godot `godot-mono` 4.7.stable**, target **Android** with **ETC2/ASTC** VRAM compression.
- The Blender→glTF→Godot **round trip itself is Assumed, not Confirmed** — this stage is where it gets validated for a real skinned, animated character.

## Export Requirements (Blender side)

- Format: **glTF Binary (`.glb`)**.
- **Apply all transforms**; clean `1,1,1` scale; correct orientation per `Standards/Scale.md`.
- Include: mesh, materials + albedo texture(s), armature, and the eight animations.
- Exclude: the `_Ref` collection, the check camera, lights, and any working-only objects.
- Export animations as named glTF animations matching the action names (`Idle`, `Walk`, ... `Climb`).
- If discrete LODs were authored, follow the LOD-handling decision from `CHAR_Kaya_LOD.md` (separate `_LOD1` files or a single scene, per that decision).
- File name(s) per `Standards/Naming.md`: `CHAR_Kaya.glb` (+ `CHAR_Kaya_LOD1.glb` if used).

## Import Requirements (Godot side)

- Scale reads correctly: Kaya ≈ 1.70 m, 1 unit = 1 m (`Standards/Scale.md` validation gate).
- Orientation correct: she faces the intended direction; no unexpected 90° flips (reconcile Blender −Y forward with Godot −Z forward and record the result).
- Materials import as expected PBR (albedo correct, roughness/metallic as authored); apply ETC2/ASTC.
- Skeleton imports; humanoid mapping/retarget confirmed or its absence noted (`CHAR_Kaya_Rig.md`).
- All eight animations import, named correctly, loop/one-shot behaviour correct.
- Export destination inside the Godot project TBD until the Godot project layout exists — record it here once decided.

## Validation Gate (this stage completes the character's round-trip proof)

1. Export `CHAR_Kaya.glb` per the settings above.
2. Import into a Godot 4.7 `godot-mono` project.
3. Verify scale, orientation, materials, skeleton, and all animations.
4. Place under the isometric orthographic camera; verify readability from all four orientations.
5. Build and run on a **physical Android device**; verify readability, animation playback, and performance are acceptable.
6. Record confirmed export/import settings back into this document and into `docs/development/` (round-trip result), and update the Assumption/Provisional tags in the Standards documents that this proves out (Scale, Materials, BlenderRules).

## Definition of Done

- [ ] `CHAR_Kaya.glb` exported with clean transforms, correct contents, correct exclusions, correct names.
- [ ] Imports into Godot with correct scale, orientation, materials, skeleton, and all eight animations.
- [ ] Reads correctly under the isometric camera, all four orientations.
- [ ] Runs acceptably on a physical Android device.
- [ ] Confirmed settings and round-trip results recorded; affected Standards tags updated.

---

## Agent Task Brief (paste-ready)

> **Task: Export Kaya to glTF and validate the round trip into Godot.**
>
> All prior stages are approved. Do **not** hack the source to hide export bugs — fix correctly or report the round-trip issue. Do **not** mark settings Confirmed before on-device validation.
>
> **Read first:** `AGENTS.md`, `docs/Art/Standards/BlenderRules.md`, `docs/Art/Standards/Scale.md`, `docs/Art/Standards/Naming.md`, `docs/Art/Standards/Materials.md`, `docs/development/environment.md`, and the prior Kaya stage documents. This is the round-trip validation for the character.
>
> **Work incrementally, PAUSE after each step.**
> - **Step 1 — Export.** Apply transforms, export `CHAR_Kaya.glb` with mesh/materials/armature/animations, excluding refs/camera/lights. Pause.
> - **Step 2 — Import check.** Import into Godot 4.7 `godot-mono`; verify scale, orientation, materials, skeleton, animations. Report findings. Pause.
> - **Step 3 — Camera check.** Place under the isometric orthographic camera; verify all four orientations read. Pause.
> - **Step 4 — Device check.** Build and run on a physical Android device; verify readability, animation, performance. Pause.
> - **Step 5 — Record.** Write confirmed settings and results back into this doc and `docs/development/`; update affected Standards tags. Pause.
>
> **After every step:** explain what happened, list problems and compromises, and **wait for approval**. Never continue automatically. Report round-trip failures rather than working around them silently (`Standards/BlenderRules.md`).
