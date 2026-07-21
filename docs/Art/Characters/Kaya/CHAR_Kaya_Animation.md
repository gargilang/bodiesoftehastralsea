# Kaya — Animation

Specification for Kaya's **exploration animation set**, as defined on the character sheet. Produces the looping and one-shot animations used under the isometric camera.

## Prerequisite

The rig (`CHAR_Kaya_Rig.md`) must be approved: clean skeleton, clean weights, bind pose.

## Objective

The eight exploration animations from the sheet, authored to read clearly from the fixed isometric camera, correctly named, and exporting cleanly to glTF for Godot.

## Scope Fences

This stage is **animation only**.

- Do **not** change the skeleton, weights, mesh, or textures. If deformation forces a rig change, stop and report — that is a rig-stage fix.
- Do **not** author combat, sailing, or cutscene animations. Only the exploration set below.
- Do **not** rely on camera angles other than the fixed isometric orientations (`Standards/Cameras.md`).

## Inputs (read first)

1. `AGENTS.md`
2. `docs/Art/Standards/Cameras.md` — the animations must read from four fixed orientations.
3. `docs/Art/Standards/ARTSTYLE.md` — expressive, slightly exaggerated motion; readable at mobile scale.
4. `docs/Art/Standards/Naming.md` — animation/action naming.
5. `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md` — character (practical, patient, observant).
6. `docs/Art/Characters/Kaya/CHAR_Kaya_Rig.md` — what the rig supports.
7. Reference: `docs/Art/References/Characters/Kaya/kaya-charactersheet.png` (exploration animation row).

## Animation Set (from the sheet)

| Animation | Type | Notes |
| --- | --- | --- |
| `Idle` | Loop | Grounded, alive-but-calm breathing/weight shift; reflects her patient, observant character. |
| `Walk` | Loop | Steady working gait; moves along the tile grid. |
| `Run` | Loop | Faster, still grounded; readable stride from above. |
| `Interact` | One-shot | Reaching/handling a nearby object (chest, latch). Returns to idle. |
| `UseTool` | One-shot or loop | Working with a tool (wrench on machinery) — her mechanic identity. Loopable while "working". |
| `LookAround` | One-shot | Hand-to-brow scan of the horizon; observation beat. |
| `Push` | Loop | Pushing a heavy object (crate); braced, leaning stance. |
| `Climb` | Loop | Climbing a ladder/ledge; hand-over-hand. |

Action names per `Standards/Naming.md` conventions (PascalCase, no spaces): `Idle`, `Walk`, `Run`, `Interact`, `UseTool`, `LookAround`, `Push`, `Climb`.

## Requirements

- **Read from the isometric camera, all four orientations.** Poses must be legible from an elevated three-quarter angle; motion should not read only from one facing.
- **Root motion policy:** author **in place** by default (locomotion driven by code in Godot), unless an approved decision says otherwise. Keep the root/`Hips` grounded and centred; document any intentional root translation. (Provisional — confirm against how Godot moves the character.)
- **Loop cleanly** for looping animations (first and last pose match; no pop).
- **Expressive, slightly exaggerated** timing consistent with the cozy anime direction — not stiff, not floppy.
- Consistent **feet-planted** contact so she reads as grounded on tiles.
- Keep animations focused and short; no unnecessary keys.
- Frame rate: choose and record a consistent project animation FPS (Provisional — record here once decided, e.g. 24 or 30).

## Definition of Done

- [ ] All eight animations authored and correctly named.
- [ ] Each reads clearly from all four isometric orientations.
- [ ] Loops are seamless; one-shots return cleanly to a neutral pose.
- [ ] In-place root motion (or documented exception); feet grounded.
- [ ] No rig/mesh/texture changes were made.
- [ ] Consistent FPS recorded; `.blend` saved.

---

## Agent Task Brief (paste-ready)

> **Task: Animate Kaya's exploration set.**
>
> The rig is approved. Do **not** change skeleton/weights/mesh/textures. Author only the eight exploration animations. They must read from the fixed isometric camera (four orientations).
>
> **Read first:** `AGENTS.md`, `docs/Art/Standards/Cameras.md`, `docs/Art/Standards/ARTSTYLE.md`, `docs/Art/Standards/Naming.md`, `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md`, `docs/Art/Characters/Kaya/CHAR_Kaya_Rig.md`, this document. Reference: the exploration-animation row in `docs/Art/References/Characters/Kaya/kaya-charactersheet.png`.
>
> **Author one animation at a time, then PAUSE after each:** `Idle` → `Walk` → `Run` → `Interact` → `UseTool` → `LookAround` → `Push` → `Climb`.
>
> **For each animation:** author in place (grounded root unless an exception is approved), make loops seamless, keep it expressive but readable from above, save the `.blend`, explain the result, note compromises, and **wait for approval** before the next. Never continue automatically. If any animation reveals a deformation problem, stop and report it as a rig issue — do not fix it by editing weights here.
