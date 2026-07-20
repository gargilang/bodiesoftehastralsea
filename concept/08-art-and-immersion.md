# Art and Immersion

## Visual Direction and 3D Art Pipeline

The target is stylized, painterly, cozy anime rather than photorealism: Ghibli-esque impressionism without reproducing a specific film, artist, character, or studio design.

Traits include visible soft brushwork, low-poly silhouettes, restrained hand-painted textures, warm Mediterranean coastal colors, painted-looking light and shadow, atmospheric separation of sea/island/sky, expressive shapes, mobile-readable values, and danger conveyed by scale, weather, color, motion, and sound rather than gore or noise.

The Godot pipeline uses:

1. Low-poly models with broad silhouettes.
2. Hand-painted albedo with small detail painted rather than modeled.
3. Normal maps only when they improve readability.
4. Stylized water with layered color, directional normals, foam, wake masks, and current streaks.
5. Impostor or layered skies for painterly clouds and weather.
6. Baked lighting where practical with restrained real-time lights.
7. LODs, atlases, and limited transparency for Android.
8. Exaggerated animation and shader cues for wind, current, stress, and momentum.

### Reusable BlenderMCP Asset Prompt

> Create a game-ready 3D [ASSET NAME] for a stylized painterly maritime exploration game built in Godot and targeting Android. Use a low-poly construction with a strong, readable silhouette from an elevated third-person/isometric camera. The art direction is cozy anime background painting with Ghibli-esque impressionism, soft brush strokes, warm Mediterranean colors, hand-painted lighting, atmospheric softness, and no hard photoreal detail. Do not copy any existing film, character, prop, or studio design. Use clean topology, sensible pivots, real-world-consistent scale, and separate only the parts that need animation or interaction: [MOVING/INTERACTIVE PARTS]. UV unwrap efficiently and create hand-painted albedo textures with broad color variation, subtle edge wear, and restrained roughness. Avoid dense geometry, tiny surface noise, heavy transparency, and expensive materials. The asset must remain readable on a small screen and under changing weather. Provide named objects, collision-friendly geometry, a simple LOD plan, and export-ready transforms for glTF/GLB import into Godot.

Asset prompts should also specify gameplay purpose, interaction points, dimensions, polygon budget, camera distance and angle, animations, collision, texture resolution and atlas, weathered/repaired/supernatural variants, and mobile-critical cues.

## Immersion Principles

Minimize menus or separate them from sailing. Crafting, upgrades, chart planning, cargo organization, routing, and equipment selection generally happen at port, workbenches, navigation tables, in the cabin, or before departure.

Active sailing avoids HUD clutter. Information comes through engine sound, wind ribbons, rope tension, mast strain, heel, flooding, compass movement, stars, clouds, animals, radio static, lantern flicker, and sea color.

The player should feel present on the boat rather than detached behind an interface.
