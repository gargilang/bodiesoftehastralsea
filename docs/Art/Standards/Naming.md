# Naming Standard

Reference specification for naming objects, collections, files, materials, and exports. Consistent naming keeps assets traceable, keeps Godot imports predictable, and lets agents find and reference things without guessing.

## Principles

- Descriptive over clever. A name should say what the thing is and which asset it belongs to.
- Stable prefixes group related items and sort predictably.
- No spaces in names that become file paths, node names, or Godot resource identifiers. Use `PascalCase` or `snake_case` consistently as specified below.
- Match the reference-document naming (for example `CHAR_Kaya_...`) so docs, files, and Blender data line up.

## Asset Prefixes

| Prefix | Meaning | Example |
| --- | --- | --- |
| `CHAR_` | Character | `CHAR_Kaya` |
| `BOAT_` | Boat / vessel | `BOAT_CelestialVista` |
| `ENV_` | Environment piece | `ENV_Dock`, `ENV_House` |
| `PROP_` | Small prop | `PROP_Crate`, `PROP_Lantern` |

## Blender Object Names

Format: `PREFIX_AssetName_Part`

- `CHAR_Kaya_Body`
- `CHAR_Kaya_Jacket`
- `CHAR_Kaya_Satchel`
- `CHAR_Kaya_ToolPouch`
- `CHAR_Kaya_Boots`

Use `PascalCase` for the `AssetName` and `Part` segments. Keep parts separated only when they must be separate for animation, interaction, or material reasons (see `Standards/BlenderRules.md`); otherwise keep the mesh joined.

## Collections

Format: `PREFIX_AssetName` as the top collection, with stage or logical sub-collections as needed.

```
CHAR_Kaya
├── CHAR_Kaya_Mesh
├── CHAR_Kaya_Rig        (added at rig stage)
└── CHAR_Kaya_Ref        (image references / check camera; never exported)
```

## Armature and Bones

- Armature object: `CHAR_Kaya_Armature`.
- Bone names: descriptive, side-suffixed where paired, following a standard humanoid convention. Provisional convention (confirm against Godot humanoid retarget needs at rig stage):
  - Center: `Hips`, `Spine`, `Chest`, `Neck`, `Head`.
  - Paired, side-suffixed: `UpperArm_L` / `UpperArm_R`, `LowerArm_L` / `LowerArm_R`, `Hand_L` / `Hand_R`, `UpperLeg_L` / `UpperLeg_R`, `LowerLeg_L` / `LowerLeg_R`, `Foot_L` / `Foot_R`.
- Keep side suffixes as `_L` / `_R` for consistency and to support Blender's symmetry tools.

## Materials

Format: `PREFIX_AssetName_MatName`

- `CHAR_Kaya_Skin`
- `CHAR_Kaya_Jacket`
- `CHAR_Kaya_Cloth`
- `CHAR_Kaya_Leather`
- `CHAR_Kaya_Metal`

Keep material count low and shared where possible; material and atlas rules live in `Standards/Materials.md`.

## Textures / Images

Format: `PREFIX_AssetName_Map_Resolution.ext`

- `CHAR_Kaya_Albedo_2048.png`
- `CHAR_Kaya_Normal_2048.png` (only if a normal map is used)

## Blend Files

Format: `PREFIX_AssetName_Stage.blend`, stored in the asset's working directory (working-file location to be confirmed when the project source layout is decided).

- `CHAR_Kaya_Modeling.blend`
- `CHAR_Kaya_Rig.blend`
- `CHAR_Kaya_Anim.blend`

One authoritative editable `.blend` per stage, or a single progressive file — decide per asset and record the choice in the asset's Overview document. Keep the editable source; never treat an export as the source.

## Export Files

Format: `PREFIX_AssetName.glb` (or `_LOD0`, `_LOD1` suffixes when LODs are exported separately).

- `CHAR_Kaya.glb`
- `CHAR_Kaya_LOD1.glb`

Export destination inside the Godot project is defined in the asset's Export document and confirmed once the Godot project layout exists.

## Reference Images

Stored under `docs/Art/References/`, mirroring the doc structure. Descriptive names:

- `References/Characters/Kaya/kaya-charactersheet.png`

## Do Not

- Do not leave default names (`Cube`, `Cube.001`, `Material.002`, `Armature`).
- Do not use spaces, accented characters, or punctuation other than `_` in names that reach files or Godot.
- Do not rename established bones or objects after rigging/animation without updating dependent stages.
