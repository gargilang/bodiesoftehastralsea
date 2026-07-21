# Materials Standard

Reference specification for materials and textures. Source: `docs/concept/08-art-and-immersion.md` and `Standards/ARTSTYLE.md`.

The style is painterly low-poly: **geometry carries the silhouette, texture carries everything else.** Materials must stay cheap enough for Android and readable at mobile scale.

## Principles

- **Hand-painted albedo is the primary channel.** Colour, brushwork, wear, and small detail are painted into the albedo, not modelled and not simulated with expensive shading.
- **Few materials per asset.** Prefer one shared material with one albedo atlas over many separate materials. Fewer materials means fewer draw calls on mobile.
- **Restrained roughness.** Surfaces read as softly painted, not glossy or physically accurate.
- **Normal maps only when they earn their place.** Use a normal map only where it measurably improves readability (for example a strongly grooved surface seen up close). Do not add normal maps as a default. (Provisional — validate cost/benefit on-device.)
- **No expensive material features.** Avoid heavy transparency, refraction, subsurface scattering, parallax, and stacked shader nodes. (Provisional budget; confirm on-device.)

## Albedo Texturing

- Paint broad colour variation and soft brushwork.
- Add subtle edge wear and dirt where it tells a story (worn boot toes, oil on the tool pouch), kept low-frequency so it survives at mobile scale.
- Bake or paint soft ambient occlusion into the albedo where it reinforces form, consistent with the painted-lighting direction in `Standards/ARTSTYLE.md`.
- Keep a consistent implied light direction across the atlas.

## Texture Resolution (provisional budgets)

All values are **Provisional** starting targets, to be validated on the target Android hardware and adjusted. See the validation gate below.

| Asset class | Albedo target | Notes |
| --- | --- | --- |
| Main character (Kaya) | **1024 or 2048** albedo | Start at 1024; move to 2048 only if readability at camera distance demands it. Hero asset gets the larger budget if any asset does. |
| Boat (large, boardable) | 2048 (possibly atlased in parts) | Defined when boat is designed. |
| Environment module | 512–1024, atlased | Many pieces share one atlas. |
| Small prop | 256–512, atlased | Group props into shared atlases. |

- Prefer **power-of-two** dimensions.
- Godot Android build uses **ETC2/ASTC** VRAM compression (Confirmed in `docs/development/environment.md`); author textures knowing they will be compressed, and check for compression artefacts on-device.

## Atlasing

- Group related parts onto a shared UV atlas to reduce material and draw-call count.
- Characters: aim for a **single albedo atlas** covering body, clothing, and gear where practical.
- Environment and props: atlas by set (for example all dock pieces on one atlas).
- UV layout rules and per-asset atlas plans live in each asset's Texturing document.

## Roughness / Metallic

- **Roughness:** mostly high (matte/soft). Introduce lower roughness only for genuinely reflective materials (wet metal fittings, glass), and keep it subtle.
- **Metallic:** near zero for almost everything. Reserve true metallic only for small metal fittings, and even then prefer painting the look into albedo over relying on the metallic channel.
- A packed roughness (or roughness+metallic+AO) map may be introduced later if needed; treat it as optional and document it per asset. (Provisional.)

## Godot Import Expectations

- Materials author cleanly into glTF standard PBR channels so Godot imports them without hand-fixing. Confirm channel mapping on first import (part of the round-trip validation).
- Keep material names per `Standards/Naming.md`.
- The Blender→glTF→Godot material round trip is currently **Assumed**, not confirmed (`docs/development/environment.md`). Validate before treating any material setup as final.

## Validation Gate

Before texture budgets and material features are **Confirmed**:

1. Import a textured asset (Kaya, or a representative textured test mesh) into Godot with ETC2/ASTC compression.
2. View it on a physical Android device under the isometric camera.
3. Confirm readability, acceptable memory use, and absence of severe compression artefacts at the chosen resolution.
4. Update the resolution table and normal-map policy with the confirmed results.
