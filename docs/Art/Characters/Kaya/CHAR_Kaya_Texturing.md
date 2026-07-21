# Kaya — Texturing

Specification for Kaya's **UV unwrap and hand-painted albedo**. Produces the painterly surface for the approved base mesh.

## Prerequisite

The base mesh (`CHAR_Kaya_Modeling.md`) must be **approved and final**. Do not texture an unapproved mesh; re-unwrapping after mesh changes wastes work.

## Objective

An efficient UV layout and a hand-painted albedo (single shared atlas where practical) that gives Kaya her painterly, mobile-readable look and her sheet palette, ready for rigging and export.

## Scope Fences

This stage is **UV unwrap + albedo painting (+ optional normal only if justified)**.

- Do **not** rig or animate.
- Do **not** change the mesh silhouette or topology. Minor UV-driven seam tweaks only; report anything larger.
- Do **not** add expensive material features (transparency, subsurface, parallax, heavy node trees).
- Do **not** author high-frequency noise that vanishes at mobile scale.

## Inputs (read first)

1. `AGENTS.md`
2. `docs/Art/Standards/ARTSTYLE.md` — painterly, restrained, readable.
3. `docs/Art/Standards/Materials.md` — albedo-first, few materials, atlas, roughness, resolution budgets, ETC2/ASTC.
4. `docs/Art/Standards/Naming.md` — material/texture names.
5. `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md` — palette and gear.
6. Reference: `docs/Art/References/Characters/Kaya/kaya-charactersheet.png`.

## UV Requirements

- Unwrap **efficiently**: minimal wasted UV space, sensible seams hidden where the camera does not look.
- Aim for a **single albedo atlas** covering body, clothing, and gear where practical (`Standards/Materials.md`).
- No unintended overlapping UVs. Mirror only intentionally and document it.
- Consistent texel density across the character so painted detail reads evenly.

## Albedo Painting

- **Hand-painted albedo is the primary channel.** Paint broad colour, soft brushwork, and form.
- Use the sheet palette (`CHAR_Kaya_Overview.md`): teal jacket + satchel, cream shirt, dark navy trousers, brown leather, rust-red headband accent, warm skin.
- Paint soft implied lighting/AO consistent with a single light direction (`Standards/ARTSTYLE.md`).
- Add low-frequency wear that tells her mechanic story (worn boot toes, oil on the pouch, faded jacket edges) — subtle, survives at mobile scale.
- Keep the **rust-red headband** as the readable accent spot; do not scatter competing saturated colours.
- Paint the small anchor emblem on the jacket as texture, not geometry.

## Materials

- Few materials, shared where possible (`Standards/Materials.md`); prefer one atlas material for the character.
- Near-zero metallic; mostly high roughness. Subtle lower roughness only on genuinely reflective bits (metal fittings, wet leather) if included at all.
- Names per `Standards/Naming.md` (`CHAR_Kaya_Skin`, `CHAR_Kaya_Cloth`, `CHAR_Kaya_Leather`, etc., or a single `CHAR_Kaya_Atlas` material).

## Resolution (Provisional)

- Start at **1024** albedo; move to **2048** only if readability at camera distance demands it (`Standards/Materials.md`). Kaya is the hero asset and may justify 2048.
- Power-of-two. Author knowing ETC2/ASTC compression on Android; check for artefacts on-device.
- Texture file names per `Standards/Naming.md` (`CHAR_Kaya_Albedo_1024.png` / `_2048.png`).

## Normal Map

- **Only if justified.** Default is no normal map (`Standards/Materials.md`). If a specific area measurably improves readability with one, document the reason before adding it.

## Definition of Done

- [ ] Efficient UVs, single atlas where practical, consistent texel density, no accidental overlaps.
- [ ] Hand-painted albedo matching the sheet palette and painterly direction.
- [ ] Rust-red accent preserved; no competing saturation.
- [ ] Low-frequency wear only; no mobile-killing noise.
- [ ] Few, correctly named materials; near-zero metallic, mostly high roughness.
- [ ] Resolution within provisional budget; power-of-two.
- [ ] `.blend` saved; any normal-map or resolution decision recorded.

---

## Agent Task Brief (paste-ready)

> **Task: UV unwrap and hand-paint Kaya's albedo.**
>
> The base mesh is approved and final. Do **not** change topology or silhouette, do **not** rig/animate, do **not** add expensive material features or high-frequency noise.
>
> **Read first:** `AGENTS.md`, `docs/Art/Standards/ARTSTYLE.md`, `docs/Art/Standards/Materials.md`, `docs/Art/Standards/Naming.md`, `docs/Art/Characters/Kaya/CHAR_Kaya_Overview.md`, this document. Reference: `docs/Art/References/Characters/Kaya/kaya-charactersheet.png`.
>
> **Work incrementally. Complete one step, then PAUSE.**
> - **Step 1 — UV unwrap.** Efficient seams, single atlas where practical, even texel density. Pause.
> - **Step 2 — Base colours.** Block the sheet palette flat across the atlas. Pause.
> - **Step 3 — Painterly shading.** Add soft brushwork, implied light/AO, form. Pause.
> - **Step 4 — Wear and accents.** Low-frequency wear, anchor emblem, preserve the rust-red accent. Pause.
> - **Step 5 — Material + resolution.** Assign the (few) materials, set roughness/metallic, confirm resolution. Pause.
>
> **After every step:** save the `.blend`, explain what changed, list compromises, and **wait for approval**. Never continue automatically. Report conflicts rather than resolving silently.
>
> **Validation note:** confirm readability and compression on a physical Android device before treating resolution/normal choices as final (`Standards/Materials.md`).
