# Kaya — Modeling

Specification for Kaya's **base mesh**. This stage produces a clean, game-ready low-poly mesh matching the character sheet. Nothing else.

## Objective

A single, clean, low-poly, quad-based base mesh of Kaya with a strong readable silhouette from the isometric camera, at correct scale, correctly named and organized, with a feet-centred origin — ready to be UV unwrapped and textured in a later stage.

## Scope Fences

This stage is **modeling only**.

- Do **not** rig.
- Do **not** animate.
- Do **not** texture or paint.
- Do **not** UV unwrap (that is the Texturing stage).
- Do **not** sculpt high-frequency detail.
- Do **not** create materials beyond simple flat viewport colours for readability.

If you believe an earlier decision blocks the mesh, stop and report it. Do not resolve it by drifting into another stage.

## Inputs (read first, in order)

1. `AGENTS.md` (repository root) — safety, MCP rules, workflow.
2. `docs/Art/Standards/ARTSTYLE.md` — painterly low-poly direction, readability.
3. `docs/Art/Standards/Scale.md` — 1 unit = 1 m, ≈1.70 m height, tile grid.
4. `docs/Art/Standards/Cameras.md` — isometric orthographic camera, four orientations.
5. `docs/Art/Standards/BlenderRules.md` — topology, pivots, transforms, safety.
6. `docs/Art/Standards/Naming.md` — object/collection/material names.
7. `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md` — identity, silhouette, gear, palette.
8. Reference image: `docs/Art/References/Characters/Kaya/kaya-charactersheet.png`.

## Reference Use

Match from the sheet: overall silhouette, proportions, hairstyle (tied back + headband), clothing layers (open jacket over shirt, cuffed trousers, boots), and equipment placement (tool pouch at belt, crossbody satchel). Do **not** copy painterly surface detail — that is painted later.

## Mesh Requirements

### Proportion and scale
- Standing height ≈ **1.70 m** (`Standards/Scale.md`).
- Slightly stylized, grounded proportions consistent with cozy anime (`Standards/ARTSTYLE.md`) — not photoreal anatomy, not chibi. Confirm exact head-count proportion against the sheet.
- Neutral stance suitable for rigging (roughly A-pose or relaxed T-pose — choose and note which; A-pose is generally preferred for shoulder deformation).

### Topology
- Low-poly, **quad-dominant**, clean.
- Edge loops at deforming joints (shoulders, elbows, hips, knees, ankles, neck) — enough to bend, no more.
- No n-gons on deforming areas; limited tris only on rigid parts.
- No doubled vertices, no interior faces the camera never sees.
- No high-frequency detail; small detail is painted later.

### Silhouette
- Must read as a clean solid shape from all **four** isometric orientations (`Standards/Cameras.md`).
- Gear reads as a few bold shapes (satchel, pouch, jacket layering), not many tiny ones.

### Structure and separation
Keep the mesh **joined** except where a part genuinely needs to be separate for later animation, interaction, or a material that cannot share the atlas (`Standards/BlenderRules.md`). Likely separate objects (confirm during work):
- `CHAR_Kaya_Body` (body + integrated clothing where it deforms with the body)
- `CHAR_Kaya_Satchel` (rigid, hangs from a strap — candidate for separate object / later attachment)
- `CHAR_Kaya_ToolPouch` (rigid belt item — candidate for separate object)

Record the final separation decision and reasoning at the end of the task.

### Pivot / origin
- Origin at the **feet, centred on the ground plane** (`Standards/BlenderRules.md`), so Kaya plants on the tile grid at ~1.70 m.

### Transforms
- Apply location, rotation, and scale (clean `1,1,1` scale, clean rotation) before finishing.
- No negative scale; normals recalculated outside.

### Naming and organization
- Names per `Standards/Naming.md` (`CHAR_Kaya_*`).
- Collections: `CHAR_Kaya` → `CHAR_Kaya_Mesh`, plus a non-exported `CHAR_Kaya_Ref` for references / check camera.
- Simple flat viewport colours only, so parts are distinguishable — not real materials.

## Polygon Budget (Provisional)

Target **~3,000–8,000 triangles** for the base mesh (hero character; the sheet's readability is silhouette-driven, so most tris go to the body and clothing large forms). This is a **Provisional** starting budget from `Standards/Materials.md`/style intent, **not validated on-device**. Stay in range; if the silhouette demands more, report it rather than silently exceeding.

## Definition of Done (this stage)

- [ ] Mesh matches the sheet's silhouette, proportions, hairstyle, clothing layers, and gear placement.
- [ ] Clean quad-dominant topology with loops at deforming joints.
- [ ] Height ≈ 1.70 m; 1 unit = 1 m.
- [ ] Reads cleanly from all four isometric orientations.
- [ ] Origin at feet on the ground plane.
- [ ] Transforms applied; scale `1,1,1`; normals correct.
- [ ] Objects, collections, and placeholder viewport colours named per standard.
- [ ] Triangle count within (or explicitly-reported deviation from) the provisional budget.
- [ ] `.blend` saved; separation decisions recorded in `CHAR_Kaya_Overview.md`.

---

## Agent Task Brief (paste-ready)

> **Task: Create the base mesh for Kaya.**
>
> We are creating the playable protagonist for an isometric Android game, *Bodies of the Astral Sea*. Today we create **ONLY the base mesh**.
>
> **Do NOT** rig, animate, texture, UV unwrap, sculpt high-frequency detail, or create materials beyond simple flat viewport colours.
>
> **Read first, in this order:**
> 1. `AGENTS.md`
> 2. `docs/Art/Standards/ARTSTYLE.md`
> 3. `docs/Art/Standards/Scale.md`
> 4. `docs/Art/Standards/Cameras.md`
> 5. `docs/Art/Standards/BlenderRules.md`
> 6. `docs/Art/Standards/Naming.md`
> 7. `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md`
> 8. This document.
>
> These documents are the source of truth. If Standards and this document ever disagree, Standards win — and report it.
>
> **Concept reference:** `docs/Art/References/Characters/Kaya/kaya-charactersheet.png`. Match silhouette, proportions, hairstyle, clothing layers, and equipment placement. Do not copy painterly surface detail.
>
> **Work incrementally. Complete one step, then PAUSE.**
>
> - **Step 1 — Body blockout.** Build the body from simple primitive forms at ~1.70 m, neutral stance. Pause.
> - **Step 2 — Refine proportions.** Match the sheet's proportions and stance. Pause.
> - **Step 3 — Clothing.** Model the jacket layering, shirt, cuffed trousers, boots as low-poly form. Pause.
> - **Step 4 — Accessories.** Model the tool pouch, satchel + strap, headband/hair. Decide and record what is separate vs. joined. Pause.
> - **Step 5 — Silhouette check.** Verify the read from all four isometric orientations using the check camera. Pause.
> - **Step 6 — Topology optimization.** Clean topology, add joint loops, remove waste, set feet origin, apply transforms, fix normals. Pause.
> - **Step 7 — Organize.** Names, collections, placeholder viewport colours per standard. Pause.
>
> **After every step:** save the `.blend`, explain what changed, list any compromises, report the current triangle count, and **wait for approval** before continuing. Never continue automatically. Report conflicts with the concept or Standards rather than resolving them silently.

## Notes for Next Stage

Modeling output feeds `CHAR_Kaya_Texturing.md` (UV unwrap + hand-painted albedo). Do not begin it in this task.
