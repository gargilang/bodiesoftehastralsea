# Scale Standard

Reference specification for real-world-consistent scale across all 3D art. Consistent scale is required so assets, cameras, animation, and physics behave predictably after glTF import into Godot.

## Unit Convention

- **1 Blender unit = 1 metre.** (Provisional decision — standard for Godot glTF workflows; to be confirmed once the Blender→glTF→Godot round trip is validated.)
- Blender scene unit system: **Metric**, unit scale **1.0**.
- Godot imports glTF at 1 unit = 1 metre, so no scale conversion is expected. Validate on first import.
- All assets are modelled at true scale. Do not model "large" and shrink on export.

## Orientation

- **Up axis:** Blender Z-up during authoring.
- **Forward axis:** character and directional assets face **−Y** in Blender (so the character looks along −Y). (Provisional; confirm the convention holds through glTF import into Godot's −Z forward and document the resulting rotation once tested.)
- Apply all transforms before export (see `Standards/BlenderRules.md`). Exported transforms must be clean.

## Character Reference Height

- **Kaya:** target standing height **≈ 1.70 m** (top of head, boots flat, neutral stance). (Provisional — chosen as a plausible adult height for a 26-year-old character; adjust only with an owner decision.)
- Model her at this height in metres so she sits correctly on the tile grid and against environment assets.
- Eyeline and proportion notes live in `Characters/Kaya/CHAR_Kaya_Modeling.md`.

## Ground and Tile Grid

The isometric land environment reads as a tiled surface (stone dock blocks in the reference art). To keep characters, props, and environment aligned:

- **Tile module:** **1.0 m × 1.0 m** ground tile as the base grid unit. (Provisional — derived from the dock-block reference; confirm against environment blockout when Environment assets are designed.)
- Place asset pivots so they align to this grid (see pivot rules in `Standards/BlenderRules.md`).
- Vertical block/step height and multi-tile modules will be defined in the Environment documents when those assets are designed. Until then, treat only the 1 m horizontal module as settled.

## Relative Scale Sanity Checks

When modelling any asset, sanity-check it against these approximate references (all provisional targets):

| Element | Approx. size | Notes |
| --- | --- | --- |
| Kaya (standing) | 1.70 m tall | Primary human reference. |
| Ground tile | 1.0 m square | Base grid module. |
| Doorway (house) | ~2.0 m tall | Human must pass comfortably. |
| Boat (CelestialVista) | TBD | Defined when the boat is designed; must dwarf a single tile and read as boardable. |

Do not finalize environment or boat scale from this table alone; it exists to catch gross scale errors during character work.

## Validation Gate

Before scale is treated as **Confirmed** rather than **Provisional**:

1. Export a simple 1 m reference cube and Kaya's blockout to glTF.
2. Import both into Godot.
3. Confirm the cube reads as 1 m and Kaya reads as ~1.70 m under the isometric camera without manual rescaling.

Record the result in `docs/development/` (a decision record or environment note) and update this document.
