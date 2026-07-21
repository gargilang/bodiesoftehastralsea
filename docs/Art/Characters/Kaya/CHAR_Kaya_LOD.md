# Kaya — LOD (Level of Detail)

Specification for Kaya's **level-of-detail tiers** for the real-time 3D pipeline on Android.

## Context

Kaya is a real-time low-poly 3D character (Provisional decision; `CHAR_Kaya_Overview.md`). Under the isometric camera she is usually at a fairly consistent distance, so aggressive LOD may be unnecessary — but a plan exists so performance work is deliberate, not ad hoc.

## Prerequisite

Approved mesh, textures, rig. LOD is derived from the final LOD0 asset.

## Objective

A small set of LOD meshes (or a decision that fewer are needed) that reduce cost at distance without visibly harming the silhouette at the camera's actual range, plus a documented triangle count per tier.

## Scope Fences

- Do **not** change LOD0's silhouette, rig, or textures.
- Do **not** produce more LOD tiers than the camera range justifies. Over-LODing a fixed-distance isometric character is wasted work.
- Do **not** treat any LOD triangle number as final before on-device validation.

## Inputs (read first)

1. `docs/Art/Standards/Cameras.md` — the isometric camera distance/range that determines how many LODs matter.
2. `docs/Art/Standards/ARTSTYLE.md` — silhouette-first readability.
3. `docs/Art/Standards/BlenderRules.md` — clean topology, transforms.
4. `docs/Art/Characters/Kaya/CHAR_Kaya_Modeling.md` — LOD0 budget.
5. `docs/Art/Characters/Kaya/CHAR_Kaya_Export.md` — how LODs are exported/handled in Godot.

## LOD Tiers (Provisional)

| Tier | Purpose | Triangle target | Notes |
| --- | --- | --- | --- |
| **LOD0** | Normal gameplay distance | ~3,000–8,000 (the base mesh) | The mesh from `CHAR_Kaya_Modeling.md`. |
| **LOD1** | Farther / many-on-screen | ~40–60% of LOD0 | Reduce interior/hidden detail first; preserve silhouette and gear reads. |
| **LOD2** (optional) | Very far / crowd | ~15–30% of LOD0 | Only if profiling shows a need; may be unnecessary for a fixed-distance camera. |

All numbers are **Provisional** and **not validated on-device**. The isometric camera's fixed range may make LOD1/LOD2 unnecessary — decide based on measurement, not assumption.

## Requirements

- Each LOD must preserve the **silhouette and key gear reads** (jacket layering, satchel, pouch, headband accent) at the range it is used.
- Reduce hidden/interior/rarely-seen geometry first.
- Keep UVs compatible so the same albedo atlas is reused across LODs (no separate textures per LOD).
- Rig/skinning must remain valid on reduced meshes if LODs are skinned (confirm approach against Godot import — a static far LOD may be acceptable if it is far enough to not animate visibly; document the choice).
- Naming per `Standards/Naming.md` (`CHAR_Kaya_LOD1`, `CHAR_Kaya_LOD2`).

## Godot Handling (Provisional / Assumption)

How LODs are consumed in Godot (Godot's automatic mesh LOD vs. authored discrete LOD meshes vs. `VisibilityRange`) is **not yet validated** here. Prefer letting Godot's automatic mesh LOD handle simple cases if the round trip supports it; author discrete LODs only if measurement shows automatic LOD is insufficient. Confirm during the export round trip and record the decision.

## Definition of Done

- [ ] A measured decision on how many LOD tiers Kaya actually needs at the camera's real range.
- [ ] Any authored LODs preserve silhouette and gear reads and reuse the LOD0 atlas.
- [ ] Triangle counts recorded per tier.
- [ ] Godot LOD-handling approach chosen and recorded (pending round-trip validation).
- [ ] `.blend` saved.

---

## Agent Task Brief (paste-ready)

> **Task: Plan and, if justified, build Kaya's LODs.**
>
> LOD0 (mesh + textures + rig) is approved. Do **not** change LOD0's silhouette, rig, or textures. Do **not** over-produce LODs — a fixed-distance isometric character may need few or none.
>
> **Read first:** `docs/Art/Standards/Cameras.md`, `docs/Art/Standards/ARTSTYLE.md`, `docs/Art/Standards/BlenderRules.md`, `docs/Art/Characters/Kaya/CHAR_Kaya_Modeling.md`, `docs/Art/Characters/Kaya/CHAR_Kaya_Export.md`, this document.
>
> **Work incrementally, PAUSE after each step.**
> - **Step 1 — Assess need.** From the isometric camera range, recommend how many LOD tiers Kaya actually needs (possibly zero beyond LOD0 + Godot auto-LOD). Present the recommendation. Pause.
> - **Step 2 — Build approved LODs.** If approved, build LOD1 (and LOD2 if justified), preserving silhouette and reusing the atlas. Pause.
> - **Step 3 — Godot handling decision.** Recommend automatic vs. discrete LOD, to be confirmed at export round trip. Pause.
>
> **After each step:** save the `.blend`, report triangle counts, explain choices and compromises, and **wait for approval**. Never continue automatically. Treat all triangle numbers as provisional pending on-device validation.
