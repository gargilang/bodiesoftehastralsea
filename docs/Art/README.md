# Art Documentation

Source-of-truth documentation for producing 3D art for *Bodies of the Astral Sea* in Blender, primarily through BlenderMCP under agent assistance.

These documents exist so that any agent (or human) producing art works from the same rules, budgets, references, and pipeline instead of improvising. When implementation constraints conflict with what is written here, **report the conflict; do not silently deviate** (see `AGENTS.md`).

## Evidence Status

Per `AGENTS.md`, every technical statement in these documents is tagged as one of:

- **Confirmed** — observed on the current development PC or proven by a test.
- **Owner decision** — explicitly chosen by the project owner.
- **Provisional decision** — preferred direction awaiting technical validation.
- **Assumption** — plausible but not yet verified.
- **Unknown** — must be discovered before proceeding.

The primary rendering approach documented here (real-time low-poly 3D under a fixed isometric orthographic camera, for both characters and world) is a **Provisional decision**. It has not been validated on the target Android hardware. See `Standards/BlenderRules.md` and `Characters/Kaya/CHAR_Kaya_LOD.md` for the associated validation gates.

## How Agents Use These Documents

1. Read `AGENTS.md` at the repository root first — it governs safety, dependencies, MCP use, and workflow.
2. Read the relevant `Standards/` documents. They are pure reference specifications and always take precedence over an individual asset document if the two disagree.
3. Read the asset document for the specific stage you are working on (for example `Characters/Kaya/CHAR_Kaya_Modeling.md`).
4. Load the reference image(s) named in that document from `References/`.
5. Follow the **Agent Task Brief** at the end of the asset document. It defines the exact scope, the read-first order, incremental steps, and the pause / save / explain / await-approval loop.

Do not begin a later stage (rig, texture, animation, export) while working a task scoped to an earlier stage. Each stage has its own document and its own approval.

## Document Types

- **Standards documents** are reference specifications: facts, rules, and numbers. They contain no task instructions.
- **Asset documents** (Characters, Boats) are per-stage specifications that end with a ready-to-use Agent Task Brief.

## Structure

```
docs/Art/
├── README.md                  This file.
├── References/                Reference images, mirrored to the doc structure.
│   └── Characters/Kaya/kaya-charactersheet.png
├── Standards/                 Reference specs (precedence over asset docs).
│   ├── ARTSTYLE.md            Painterly low-poly visual direction.
│   ├── Scale.md               World units, character height, tile grid.
│   ├── Cameras.md             Isometric orthographic camera and orientations.
│   ├── Naming.md              Object, collection, file, and material naming.
│   ├── Materials.md           Hand-painted albedo, roughness, atlasing.
│   ├── BlenderRules.md        Topology, pivots, transforms, MCP safety, glTF.
│   └── SpritePipeline.md      OPTIONAL future optimization (not the default path).
├── Characters/Kaya/           The protagonist, fully documented.
├── Boats/CelestialVista/      Placeholder stubs (not yet designed).
└── Environment/               Placeholder stubs (not yet designed).
```

## References Convention

- `References/` mirrors the documentation structure (`Characters/`, `Boats/`, `Environment/`, `Standards/`).
- Each asset document names the exact reference file(s) it uses by relative path.
- Reference images are visual guides. Match silhouette, proportion, and design; do not attempt to reproduce painterly surface detail as geometry.
- Keep reference images traceable: one canonical location, descriptive filenames.

## Current Scope

- **Fully written:** all `Standards/` documents and all `Characters/Kaya/` documents.
- **Stubs only:** `Boats/CelestialVista/` and `Environment/` — these assets are not yet designed, so their documents are placeholders and must not be treated as specifications.

## Source Material

These documents draw on, and must stay consistent with:

- `docs/concept/07-camera-mobile-and-accessibility.md` — camera and perspective.
- `docs/concept/08-art-and-immersion.md` — visual direction and Godot/Android pipeline.
- `docs/development/environment.md` — confirmed toolchain (Blender 5.1.2, `godot-mono` 4.7, Android export requirements).
- `References/Characters/Kaya/kaya-charactersheet.png` — Kaya's canonical character sheet.

If a concept document changes, update the affected art documents rather than letting them drift.
