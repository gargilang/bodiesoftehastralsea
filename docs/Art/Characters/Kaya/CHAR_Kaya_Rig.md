# Kaya — Rig

Specification for Kaya's **humanoid skeleton and skinning**. Produces a deformable rig ready for the exploration animation set.

## Prerequisite

The mesh (`CHAR_Kaya_Modeling.md`) and ideally the UVs/albedo (`CHAR_Kaya_Texturing.md`) are approved. Rigging assumes final topology; changing the mesh after skinning forces re-skinning.

## Objective

A clean, game-ready humanoid skeleton with correct weights, no deformation artefacts, correctly named bones, exporting cleanly to glTF and (provisionally) mapping to Godot's humanoid skeleton.

## Scope Fences

This stage is **skeleton + skinning only**.

- Do **not** animate (that is the next stage). A neutral bind pose and a couple of test poses to verify deformation are allowed, but no shipped animations.
- Do **not** change mesh topology or silhouette.
- Do **not** add complex rigging systems (IK constraints, drivers, control rigs) unless a real deformation need justifies it — game export uses the deform skeleton; keep control complexity out of what is exported.

## Inputs (read first)

1. `AGENTS.md`
2. `docs/Art/Standards/BlenderRules.md` — transforms, safety, export expectations.
3. `docs/Art/Standards/Naming.md` — armature and bone names.
4. `docs/Art/Standards/Scale.md` — scale/orientation for clean export.
5. `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md`
6. `docs/Art/Characters/Kaya/CHAR_Kaya_Animation.md` — what the rig must be able to do.

## Skeleton Requirements

- **Standard humanoid hierarchy**, kept minimal and game-ready. Provisional bone set (`Standards/Naming.md`):
  - Center: `Hips` (root of deform), `Spine`, `Chest`, `Neck`, `Head`.
  - Arms (paired `_L`/`_R`): `Shoulder`, `UpperArm`, `LowerArm`, `Hand`.
  - Legs (paired `_L`/`_R`): `UpperLeg`, `LowerLeg`, `Foot`, `Toe` (toe optional; include only if walk/climb needs it).
- **Optional gear bones** for separate rigid items so they follow the body: a bone for the satchel and one for the tool pouch, parented to the appropriate spine/hip bone. Add only if those items are separate objects (per the modeling separation decision).
- **Finger bones:** likely **not** needed at the isometric camera distance. Default to no finger bones unless the `Use Tool` / `Interact` animations demonstrably require them — report before adding.
- Armature object: `CHAR_Kaya_Armature`.

## Bone Budget (Provisional)

Target **under ~40 bones** total (`Standards/Materials.md` budget philosophy). A humanoid without fingers lands well under this. Provisional; validate on-device. Report if a real need pushes over.

## Skinning Requirements

- Smooth skinning with sensible weights; clean bends at shoulders, elbows, hips, knees, neck.
- Prefer a low **max influences per vertex** (target ≤ 4) for mobile skinning efficiency. (Provisional; confirm against Godot import.)
- No stray weights, no vertices unintentionally weighted to distant bones.
- Rigid separate items (satchel, pouch) rigidly weighted to their gear bone.
- Test deformation in a few extreme poses (bend, reach, crouch) to catch pinching — then return to bind pose. Do not keep these as animations.

## Orientation and Transforms

- Keep the forward/orientation convention from `Standards/Scale.md`.
- Apply transforms on the mesh before binding; armature at clean transform.
- Verify the rig exports to glTF without introducing rotation/scale surprises (part of the export validation, `CHAR_Kaya_Export.md`).

## Godot Humanoid Mapping (Provisional / Assumption)

Godot can retarget humanoid skeletons, but the exact bone-name mapping and whether retargeting is used is **not yet validated** here. Use conventional humanoid bone names so mapping is straightforward, and confirm during the export round trip. Report mismatches; do not invent a mapping and treat it as confirmed.

## Definition of Done

- [ ] Minimal standard humanoid skeleton, correctly named, under provisional bone budget.
- [ ] Clean skinning; no pinching at the tested joints; ≤4 influences per vertex (target).
- [ ] Gear bones (if any) rigidly driving separate items.
- [ ] Bind pose clean; test poses removed.
- [ ] Transforms clean; orientation consistent with Scale standard.
- [ ] `.blend` saved; any deviation (fingers, extra bones) recorded with reasoning.

---

## Agent Task Brief (paste-ready)

> **Task: Rig Kaya with a game-ready humanoid skeleton and skin the mesh.**
>
> The mesh (and textures) are approved and final. Do **not** animate (test poses only, then return to bind), do **not** change topology, do **not** add IK/control-rig complexity to the exported skeleton.
>
> **Read first:** `AGENTS.md`, `docs/Art/Standards/BlenderRules.md`, `docs/Art/Standards/Naming.md`, `docs/Art/Standards/Scale.md`, `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md`, `docs/Art/Characters/Kaya/CHAR_Kaya_Animation.md`, this document.
>
> **Work incrementally. Complete one step, then PAUSE.**
> - **Step 1 — Skeleton.** Build the minimal humanoid hierarchy with standard names; add gear bones only if separate items exist. Pause.
> - **Step 2 — Bind + initial weights.** Bind the mesh; generate and review automatic weights. Pause.
> - **Step 3 — Weight cleanup.** Fix joints, cap influences (target ≤4), rigidly weight gear. Pause.
> - **Step 4 — Deformation test.** Pose a few extremes to check bends; fix pinching; return to bind pose and remove test poses. Pause.
>
> **After every step:** save the `.blend`, explain what changed, list compromises, report bone count, and **wait for approval**. Never continue automatically. Report conflicts (including any need for fingers/extra bones or any Godot humanoid mapping doubt) rather than resolving silently.
