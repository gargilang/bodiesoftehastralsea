# Kaya — Character Overview

Identity, silhouette, palette, and reference for the playable protagonist. This is the entry point for all Kaya art work. Read it before any Kaya stage document.

## Reference Image

Canonical character sheet: `../../References/Characters/Kaya/kaya-charactersheet.png`

The sheet contains: front / 3-4 front / 3-4 back / back turnaround, a portrait, detail callouts (tool pouch, waterproof satchel), the colour palette, readability notes, and the isometric exploration animation set. All Kaya work derives from this sheet.

> Match silhouette, proportion, costume design, and palette. Do **not** reproduce painterly surface detail as geometry (`Standards/ARTSTYLE.md`).

## Identity (from the character sheet)

- **Name:** Kaya
- **Role:** Protagonist — Fisherman & Boat Mechanic
- **Age:** 26
- **Character:** Practical, patient, observant. Knows engines, knows the waters.
- **Weakness:** Trusts only what can be repaired, measured, or explained.
- **Beginning goal:** Live a quiet, honest life and stay out of the past.

This grounds her design: she is a working mechanic, not a glamorous adventurer. Her gear is functional and worn.

## Silhouette

Kaya must read instantly as a solid shape against sea and stone from the isometric camera (`Standards/Cameras.md`). Defining silhouette elements:

- Upright, grounded working stance.
- **Open jacket/hoodie** over a lighter shirt — layered torso reads as a clear two-tone shape.
- **Rolled/cuffed trouser legs** ending above sturdy boots.
- **Tied-back hair with a headband** — compact head shape, not a large flowing silhouette.
- **Crossbody satchel strap** and a **tool pouch/belt** breaking the waist line — key gear-on-body reads.

Keep the silhouette clean; her gear reads as a few bold shapes, not many small ones.

## Costume and Gear (from the sheet)

| Element | Description |
| --- | --- |
| Jacket | Teal / blue open work hoodie-jacket, sleeves rolled, small anchor emblem on one side. |
| Shirt | Cream / off-white shirt beneath the jacket, buttoned. |
| Trousers | Dark navy/charcoal work trousers, cuffs rolled up. |
| Boots | Brown sturdy work/leather boots. |
| Headband | Rust-red headband tying back dark, curly, tied-up hair. |
| Tool pouch | Brown leather belt pouch holding tools (wrench etc.) — mechanic identity. |
| Satchel | Teal waterproof crossbody satchel with warm star/pattern detail. |
| Skin | Warm mid-tone. |

## Colour Palette (from the sheet)

Five anchors, muted-warm with a single strong accent:

| Role | Colour | Use |
| --- | --- | --- |
| Warm accent | **Rust red** | Headband and small accents — the readable "spot" colour. |
| Light | **Cream** | Shirt; lightest value. |
| Dark | **Dark navy** | Trousers; anchors the value range. |
| Neutral | **Brown** | Leather boots, pouch, straps. |
| Cool base | **Teal** | Jacket and satchel; ties her to the sea palette. |

Readability notes from the sheet, treated as rules: clean silhouette; muted but warm accent (rust red); strong contrast against sea and stone; works well on small mobile screens. These align with `Standards/ARTSTYLE.md`.

## Exploration Animation Set (from the sheet)

The sheet defines eight isometric exploration animations. Detailed in `CHAR_Kaya_Animation.md`:

`Idle`, `Walk`, `Run`, `Interact`, `Use Tool`, `Look Around`, `Push`, `Climb`.

## Pipeline (Provisional decision)

Kaya is a **real-time low-poly 3D** character rendered under the fixed isometric orthographic camera. She is **not** baked to 2D sprites (see `Standards/SpritePipeline.md`). This is provisional and subject to the on-device validation gates noted in the stage documents.

## Stage Documents (production order)

1. `CHAR_Kaya_Modeling.md` — base mesh.
2. `CHAR_Kaya_Texturing.md` — UV + hand-painted albedo.
3. `CHAR_Kaya_Rig.md` — humanoid skeleton + skinning.
4. `CHAR_Kaya_Animation.md` — the eight exploration animations.
5. `CHAR_Kaya_LOD.md` — level-of-detail tiers.
6. `CHAR_Kaya_Export.md` — glTF/GLB export to Godot.
7. `CHAR_Kaya_SpriteRender.md` — OPTIONAL stub (not part of normal work).

Each stage is a separate task with its own approval. Do not run ahead into a later stage while working an earlier one.

## Scale

- Standing height ≈ **1.70 m** (Provisional; `Standards/Scale.md`).
- Origin at the feet on the ground plane (`Standards/BlenderRules.md`).

## Working Files

Naming per `Standards/Naming.md` (`CHAR_Kaya_*`). Record the chosen working-file strategy (progressive single file vs. per-stage files) here once decided:

- *Working-file strategy: TBD at modeling stage — record the decision in this section.*
