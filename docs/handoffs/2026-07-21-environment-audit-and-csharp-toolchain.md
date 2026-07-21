# Handoff: Development-PC audit, docs restructure, and C# toolchain install

## Status

Complete — environment audited, C# toolchain installed, and the Godot C# Android validation gate passed on a physical device. Decision 001 is now Accepted.

## Objective

Cross-check the planning documentation against the real CachyOS development PC on first development startup, confirm what is actually possible, install the missing pieces needed for the provisional C# direction, and update the docs to match reality.

## Environment

- Machine role: Development PC
- OS: CachyOS Linux, kernel 7.0.12-1-cachyos, Wayland/KDE
- Hardware: i5-12450H (12 threads), 15 GiB RAM, Intel UHD + NVIDIA RTX 3050 Laptop GPU, ~376 G free on nvme
- Godot: 4.7.stable standard (`/usr/bin/godot`) and 4.7.stable.mono (`/usr/bin/godot-mono`)
- .NET SDK: 10.0.110
- Android: OpenJDK 17.0.19, SDK build-tools 35/36/37, platforms 34–36.1, NDK 28.2.13676358, adb 35.0.2, Godot export templates 4.7.stable present
- Blender 5.1.2 (`/usr/local/bin`), Git 2.54.0 (no git-lfs), Python 3.14.5, uv 0.11.21, aria2c 1.37.0, OpenCode 1.18.4
- Target device: Vivo I2508, Android 16 (SDK 36), arm64-v8a, Snapdragon (canoe), Adreno 829, 11 GB RAM, Vulkan 1.4 — connected and USB-debugging authorized during validation

## Completed

- Ran the full read-only audit from `environment.md`; recorded confirmed values.
- Restructured the repository: moved `concept/`, `development/`, `agents/`, `handoffs/` under `docs/` via `git mv`; kept `README.md` and `AGENTS.md` at root. Updated all internal links in `README.md` and `AGENTS.md`.
- Installed the C# toolchain using the owner-directed method (aria2c download → verify → copy to `/var/cache/pacman/pkg/` → `sudo pacman -U`): `dotnet-host`, `dotnet-runtime`, `dotnet-targeting-pack`, `dotnet-sdk` (10.0.10.sdk110-1) and `godot-mono` (4.7-2.1).
- Ran the desktop C# validation headless: `dotnet build` produced `CSharpGate.dll` (0 errors); `godot-mono --headless` ran the scene and executed C#. Confirms .NET SDK 10.0.110 + Godot 4.7.mono compile/run compatibility.
- Ran the full on-device Android gate on the Vivo phone: generated a debug keystore, downloaded and installed the `.mono` Android export templates, exported a signed debug APK, installed and launched it, and confirmed hardware Vulkan rendering (Adreno 829), single-touch input, `Input.VibrateHandheld`, and `user://` save read/write via logcat. Uninstalled the disposable app afterward.
- Discovered and documented the C# Android export requirements (mono templates, `net9.0`, classic `.sln` vs .NET 10 `.slnx`, ETC2/ASTC, JDK path + keystore).
- Set editor setting `export/android/java_sdk_path=/usr/lib/jvm/java-17-openjdk`.
- Updated `docs/development/environment.md` (confirmed findings + package installation method + C# Android export requirements), `docs/development/decisions/001-godot-csharp-android.md` (**Accepted**, all gate items recorded), `docs/development/setup-and-validation.md`, and the README planning status.

## Files and Systems Changed

- Repo docs relocated into `docs/` (rename only, content preserved except the edits below).
- `README.md` — links repointed to `docs/`; Current Planning Status rewritten with confirmed environment.
- `AGENTS.md` — three doc-path references repointed to `docs/`.
- `docs/development/environment.md` — added confirmed-findings tables, package installation method, updated fact/direction tables.
- `docs/development/decisions/001-godot-csharp-android.md` — status update; gate items 1–2 marked confirmed.
- `docs/development/setup-and-validation.md` — status + Stage 1/2 updates.
- System: installed 5 packages via pacman; copies also left in `/var/cache/pacman/pkg/`. Download working copies are in `/tmp/opencode/pkgdl/`.

## Decisions Made

- Keep both `godot` (standard) and `godot-mono` (.NET) installed — they do not conflict; the C# binary is `/usr/bin/godot-mono`. Recorded in environment.md and decision 001.
- Documented the owner's aria2c → cache → `sudo pacman -U` install method and a mirror-probing step, so another PC can be brought to the same state. Recorded in environment.md.

## Validation Performed

- `godot --version` → 4.7.stable; `godot-mono --version` → 4.7.stable.mono.
- `dotnet --version` → 10.0.110; `dotnet --list-sdks`/`--list-runtimes` show SDK 10.0.110 and runtime 10.0.10.
- `zstd -t` integrity-checked all five downloaded packages before install.
- README doc links all resolve to existing files.

## Not Validated

- Explicit multitouch (only single-touch taps confirmed).
- Code-iteration time and sustained on-device frame pacing under real gameplay load.
- Beckett MCP and BlenderMCP capabilities/permissions (configured but not exercised).
- Blender `.blend` → `.glb` → Godot round trip.
- Release (non-debug) APK signing with a real keystore.
- git-lfs is not installed (may matter later for binary assets).

## Remaining Work

- Establish repository layout and C#/Godot naming conventions (Stage 6 of setup-and-validation).
- Validate Beckett MCP and BlenderMCP (Stages 4–5).
- Validate the Blender `.blend` → `.glb` → Godot asset round trip.
- Measure iteration time and frame pacing; explicitly test multitouch.
- Decide production renderer and whether git-lfs is needed before adding binary assets.

## Known Problems and Risks

- CachyOS/Arch mirrors are inconsistent: `mirror.krfoss.org` returned intermittent 403 under retry pressure; `cdn77.cachyos.org` throttled to ~1.5 KB/s; interrupted/resumed downloads produced a corrupt `godot-mono` package twice. Always verify with `zstd -t` before installing.
- Global OpenCode config (`~/.config/opencode/opencode.json`) contains a plaintext API key. Outside this repo and left untouched, but flagged — consider moving it to an env var.

## Failed or Rejected Approaches

- Multi-connection aria2 (`-x8`) against `mirror5.krfoss.org` triggered "Invalid range header" errors; single-file, verified downloads from a probed fast mirror worked.
- Resuming a partially downloaded CachyOS package left corrupt data; delete and redownload clean instead.

## Next Recommended Action

1. Begin Stage 6 prototype authorization: define the repository layout, C#/Godot naming conventions, source-asset vs. generated-export locations, and a test strategy, using the confirmed toolchain and the C# Android export requirements in environment.md.

## Relevant References

- `docs/development/environment.md`
- `docs/development/decisions/001-godot-csharp-android.md`
- `docs/development/setup-and-validation.md`

## Handoff Maintenance

Temporary operational context. Durable facts already moved into `environment.md`; durable technical choices into decision 001. Archive or replace this handoff once the on-device C# validation gate is closed.
