# Camera Standard

Reference specification for the camera framing that art must be authored and checked against. Source: `docs/concept/07-camera-mobile-and-accessibility.md`.

The camera determines silhouette, readability, and which surfaces are ever seen. Every asset must be checked from the camera defined here before it is considered done.

## Land Exploration Camera (primary for characters and environment)

**Owner decision.** Land exploration uses an **isometric / three-quarter orthographic-style** camera with **four fixed orientations that snap in 90-degree steps**. This preserves touchscreen legibility and stable framing.

Authoring implications:

- Characters and environment assets are seen from an **elevated three-quarter angle**, never from ground level and never from directly above.
- Because orientation snaps in 90° steps, an asset is potentially seen from **four cardinal directions**. All four must read cleanly. There is no permanently hidden back — the reference sheet's front / 3-4 front / 3-4 back / back views exist for exactly this reason.
- **Orthographic projection**: no perspective foreshortening. Proportions read literally, so silhouette discipline matters more than in a perspective view.

### Provisional Camera Rig (for silhouette checks in Blender)

Set up a check camera in Blender matching the game's intended framing so assets can be validated during authoring. (Provisional values — confirm and lock once the Godot camera is built and matched.)

- **Projection:** Orthographic.
- **Angle:** classic isometric-style three-quarter view. Provisional starting angles: **rotation X ≈ 60°, rotation Z ≈ 45°** (adjust to match the reference sheet's isometric poses, then lock the exact value here once confirmed).
- **Four orientations:** duplicate/rotate the camera (or the asset) in 90° Z steps to check all four facings.
- **Orthographic scale:** framed so a standing character occupies a comfortable, mobile-readable portion of the frame — not filling it, not tiny. Lock the exact value after matching the Godot camera.

This rig is a validation tool, not a shipped asset. Do not export it.

## Boat / Sea Camera (reference only; not needed for land character work)

**Owner decision.** Boat travel uses a **stable, elevated third-person camera behind and above the vessel** (readability inspired by *Dredge* and *Spilled*), keeping boat, water, horizon, and environmental vectors visible without excessive roll.

This camera is documented here for completeness. It is relevant to boat and sea assets, not to Kaya's land exploration work. Boat-specific framing will be detailed in the `Boats/` documents when that work begins.

## Rules for All Assets

- **Check the silhouette from the land camera** (all four orientations) before considering geometry final.
- **Do not detail surfaces the camera never sees closely.** The camera is elevated and distant; undersides, deep interiors, and fine top-of-head or foot-sole detail are low priority.
- **Do not optimize a shape so it only reads from one angle.** Four-orientation snapping means every cardinal facing must be correct.
- Camera distance means small painted detail is lost — reinforce important reads with silhouette and value, per `Standards/ARTSTYLE.md`.

## Validation Gate

Camera angles and orthographic scale here are **Provisional** until the Godot land-exploration camera is built and the Blender check camera is matched to it. Once matched:

1. Record the exact Godot camera parameters.
2. Update the Blender check-camera values in this document to match.
3. Re-tag the rig values as **Confirmed**.
