# Art Style Standard

Reference specification for the visual direction of all 3D art. This document has precedence over individual asset documents. Source: `docs/concept/08-art-and-immersion.md`.

## Direction

**Owner decision.** Stylized, painterly, cozy anime — Ghibli-esque impressionism — rather than photorealism. The look evokes soft brushwork and hand-painted background art without reproducing any specific film, artist, character, or studio design.

Do not copy any existing film, character, prop, or studio design. Draw on the *genre* of cozy painterly maritime art, not a particular source.

## Core Traits

- Low-poly silhouettes with broad, readable shapes.
- Visible soft brushwork expressed in **texture**, not in geometry.
- Restrained hand-painted albedo; small detail is painted, not modeled.
- Warm Mediterranean coastal color: sun-warmed stone, sea teal, terracotta, weathered wood.
- Painted-looking light and shadow; baked where practical.
- Atmospheric separation of sea, island, and sky.
- Expressive, slightly exaggerated shapes over anatomical precision.
- Values that stay readable on a small mobile screen and under changing weather.
- Danger and force conveyed by scale, weather, color, motion, and sound — never by gore or visual noise.

## Readability Rules (mobile-critical)

The game ships on Android and is viewed on a small screen from an elevated isometric camera. Every asset must obey:

- **Clean silhouette.** The shape must read instantly at small size. Test the silhouette as a solid fill against sea and stone.
- **Strong value contrast** against the two dominant backgrounds: sea (teal/blue) and stone/dock (warm neutral).
- **Muted base palette with a small number of warm accents.** Accents mark the important thing (a character, an interactable), not decoration.
- **No high-frequency surface noise.** Tiny painted or modeled detail disappears at mobile scale and costs performance.
- **Consistent lighting direction** across an asset so painted shading reads as coherent form.

## What This Style Is Not

- Not photorealistic. No physically accurate microdetail, no dense normal maps for surface grain.
- Not flat-shaded minimalism. It is low-poly *carrying painterly texture*, not untextured facets.
- Not high-contrast cel shading with hard ink outlines by default. Softness is the goal; any outline treatment is a deliberate, documented choice.
- Not busy. Restraint is the rule; empty warm space is acceptable and desirable.

## Colour

- **Owner-referenced palette anchors:** warm Mediterranean coastal tones. Per-asset palettes are defined in that asset's document.
- Keep saturation muted in base surfaces; reserve saturated color for meaningful accents.
- Character-specific palettes take their swatches from the character's reference sheet (see `Characters/Kaya/CHAR_Kaya_Overview.md`).

## Relationship to Geometry and Texture

- **Geometry** provides the silhouette and large forms only.
- **Texture** (hand-painted albedo) provides all surface character, brushwork, wear, and small detail.
- **Normal maps** are used only where they measurably improve readability, not as a default. (Provisional; see `Standards/Materials.md`.)

## Consistency

If an asset cannot satisfy both a style trait and a performance budget, that is a conflict to report, not to resolve silently. Record the tradeoff and request a decision.
