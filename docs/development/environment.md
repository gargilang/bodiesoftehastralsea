# Development Environment

## Purpose

This document records the intended development environment and separates owner-provided facts from assumptions requiring confirmation. It is not an installation script and does not authorize changes to any machine.

## Environment Status

The initial version of this document was written on a restricted Windows planning PC. A read-only audit and a C# toolchain installation were then performed directly on the CachyOS development PC on 2026-07-21. Items below are marked **Confirmed on development PC** where they were directly observed on that machine; other items remain owner-reported, assumed, or unknown.

## Confirmed on Development PC (audited 2026-07-21)

### System

| Item | Confirmed value |
| --- | --- |
| OS | CachyOS Linux (Arch-based), rolling |
| Kernel | 7.0.12-1-cachyos |
| Session | Wayland, KDE |
| CPU | 12th Gen Intel Core i5-12450H (12 threads) |
| RAM | 15 GiB total |
| Storage | `/` and `/home` on `nvme0n1p2`, 475 G total, ~376 G free |
| GPU | Intel UHD Graphics (Alder Lake) + NVIDIA GeForce RTX 3050 Laptop GPU |
| Graphics APIs | Vulkan available on both GPUs; OpenGL 4.6 (Mesa 26.1.3) |

### Engine and language toolchain

| Item | Confirmed value |
| --- | --- |
| Godot (standard) | 4.7.stable, package `godot 4.7-2.1`, binary `/usr/bin/godot` — no C# support |
| Godot (.NET) | 4.7.stable.mono, package `godot-mono 4.7-2.1`, binary `/usr/bin/godot-mono` — installed 2026-07-21 |
| GodotSharp assemblies | Present under `/usr/lib/godot-mono/GodotSharp/` |
| .NET SDK | 10.0.110 (`dotnet-sdk 10.0.10.sdk110-1`), installed 2026-07-21 at `/usr/share/dotnet` |
| .NET runtime | Microsoft.NETCore.App 10.0.10 |
| Godot export templates | `4.7.stable` installed, include `android_debug.apk` / `android_release.apk` |

### Android toolchain

| Item | Confirmed value |
| --- | --- |
| JDK | OpenJDK 17.0.19 |
| Android SDK | `~/Android/Sdk` present |
| build-tools | 35.0.0, 36.1.0, 37.0.0 |
| platforms | android-34, android-35, android-36, android-36.1 |
| NDK | 28.2.13676358 |
| adb | 1.0.41 (android-tools 35.0.2) |
| Connected device | None connected at audit time — on-device validation still outstanding |

### Content and supporting tools

| Item | Confirmed value |
| --- | --- |
| Blender | 5.1.2, at `/usr/local/bin/blender` (manual install, not pacman-managed) |
| Git | 2.54.0 |
| Git LFS | Not installed |
| Python | 3.14.5 |
| `uv` | 0.11.21 (`~/.local/bin/uv`) |
| aria2c | 1.37.0 |
| OpenCode | 1.18.4 (`~/.opencode/bin/opencode`) |
| Repository remote | GitHub `gargilang/bodiesoftehastralsea`, branch `master` |
| Beckett MCP | Configured in OpenCode as remote MCP `beckett` at `http://127.0.0.1:8770/mcp`; not running at audit time |
| BlenderMCP | Configured in OpenCode as local MCP `blender-mcp` (venv at `/home/gemi/Projects/supergemart/tools/blender-mcp-venv`); not running at audit time |

## Owner-Provided Facts

| Item | Current status | Notes |
| --- | --- | --- |
| Primary development OS | Confirmed on development PC | CachyOS Linux, kernel 7.0.12-1-cachyos, Wayland/KDE |
| Game engine | Confirmed on development PC | Godot 4.7.stable; both standard and .NET (`godot-mono`) editions installed |
| 3D content tool | Confirmed on development PC | Blender 5.1.2 |
| Primary agent interface | Owner decision | OpenCode 1.18.4 |
| Godot MCP assistance | Confirmed configured | Beckett MCP (remote MCP at loopback :8770), purchased from itch.io |
| Blender MCP assistance | Confirmed configured | BlenderMCP (local MCP via venv) |
| Python | Confirmed on development PC | Python 3.14.5 |
| `uv` | Confirmed on development PC | uv 0.11.21 |
| Product platform | Owner decision | Android only |
| Distribution | Owner intention | Initially private for friends and family; possible free/nonprofit Play Store release |
| Reachable download mirror | Owner-reported constraint | `krfoss` mirrors reachable; several other Arch and CachyOS mirrors also responded 200/206 during the 2026-07-21 download (see Package Installation Method) |
| Shell and terminal | Owner-reported | Fish and Kitty |

## Provisional Technical Direction

| Item | Status | Validation required |
| --- | --- | --- |
| Engine | Confirmed available | Godot 4.7.stable, both standard and `.mono` editions present |
| Language | Provisional | C# using Godot .NET; toolchain now installed, on-device validation still required |
| .NET SDK | Confirmed installed | .NET SDK 10.0.110; compatibility with `godot-mono` 4.7 to be validated by building a project |
| Godot edition | Confirmed | `.NET`-enabled build is `godot-mono` (`/usr/bin/godot-mono`), separate from standard `godot` |
| Android toolchain | Largely confirmed present | JDK 17, SDK build-tools/platforms, NDK 28.2, adb, and export templates present; not yet exercised end to end, no device connected |
| Renderer | Undecided | Test supported renderers on representative Android hardware |
| Blender export | Assumed | Validate an editable `.blend` to `.glb` to Godot round trip |
| Beckett MCP | Assumed operational | Validate capabilities, permissions, C# quality, and failure behavior |
| BlenderMCP | Assumed operational | Validate capabilities, permissions, source preservation, and export workflow |

## Read-Only Environment Audit

The first agent operating on the development PC should inspect and record, without installing or changing anything:

### System

- CachyOS version
- kernel version
- desktop environment
- Wayland or X11 session
- CPU, GPU, graphics driver, RAM, and available storage
- representative Android device models and specifications

### Godot

- exact version
- standard or .NET-enabled edition
- executable location only if relevant
- available rendering backends
- Android export-template status
- editor and command-line launch behavior

### C# and .NET

- installed SDKs and runtimes
- compatibility with the installed Godot release
- whether a minimal Godot C# project builds
- whether C# editor integration and reload behavior are reliable

### Android

- JDK version
- Android SDK location and installed components
- any required NDK version
- device debugging availability
- successful debug APK export, installation, and launch

### Blender and MCP tools

- Blender version and rendering backend
- BlenderMCP connection and permissions
- Beckett MCP connection and permissions
- whether either MCP can execute commands, access the network, or modify files outside the repository
- how each MCP starts and stops

### Supporting tools

- Git and Git LFS
- Python and `uv`
- OpenCode version and project-relevant configuration
- existing backup and repository-remote arrangements

## Package Installation Method (owner-directed)

On this machine, install pacman packages by downloading them first, then installing locally from the cache. Do not rely on a single mirror; download speed and availability vary and some mirrors intermittently return 403 or throttle to near zero.

Confirmed working procedure (used to install the C# toolchain on 2026-07-21):

1. Resolve the exact packages and their official download URLs, e.g. `pacman -Sp <package>` prints the full dependency URL list.
2. Probe candidate mirrors for each specific file before committing to a long download. A quick ranged request reveals both availability and speed:
   - `curl -s -o /dev/null -w "%{http_code} %{speed_download}\n" -r 0-3000000 --max-time 20 <url>`
   - A `200`/`206` means the file exists on that mirror; pick the fastest responder.
3. Download each package with `aria2c` into a working directory (interrupted or resumed CachyOS downloads produced corrupt files here, so prefer a clean download and verify).
4. Verify integrity of each `.pkg.tar.zst` with `zstd -t <file>` before trusting it.
5. Copy the verified packages into the pacman cache: `sudo cp *.pkg.tar.zst /var/cache/pacman/pkg/`.
6. Install locally: `sudo pacman -U /var/cache/pacman/pkg/<package>.pkg.tar.zst ...`.

Confirmed mirror behaviour on 2026-07-21:

- Arch `extra` (`dotnet-*`): `mirror.krfoss.org`, `mirror.fsmg.org.nz`, `mirror.2degrees.nz`, `fastly.mirror.pkgbuild.com`, and `geo.mirror.pkgbuild.com` all responded; `mirror.fsmg.org.nz` was fastest. `mirror.krfoss.org` returned intermittent 403 when hammered with retries.
- CachyOS `cachyos-extra-v3` (`godot-mono`): `us.cachyos.org`, `mirror.krfoss.org`, and `mirror5.krfoss.org` worked; `cdn77.cachyos.org` was effectively stalled (~1.5 KB/s).

The C# toolchain installed this way: `dotnet-host`, `dotnet-runtime`, `dotnet-targeting-pack`, `dotnet-sdk` (10.0.10.sdk110-1) and `godot-mono` (4.7-2.1).

## C# Android Export Requirements (confirmed 2026-07-21)

A disposable C# project was exported and run on a physical Android device (Vivo I2508, Android 16, arm64-v8a, Adreno 829). The following setup was required and is confirmed working:

1. **Mono export templates.** The `.mono` Godot build needs its own Android export templates. They are not shipped by the `godot-mono` package and are separate from the standard build's templates. Download `Godot_v4.7-stable_mono_export_templates.tpz` from the official 4.7-stable release and extract the `templates/*` files into `~/.local/share/godot/export_templates/4.7.stable.mono/`. Confirm `version.txt` reads `4.7.stable.mono`.
2. **Target framework `net9.0`.** The 4.7 Android template rejects `net8.0` and does not accept `net10.0`. Set `<TargetFramework>net9.0</TargetFramework>` in the `.csproj`; SDK 10.0.110 builds it fine.
3. **Classic `.sln` required.** Godot 4.7 looks for `<AssemblyName>.sln` in classic format. .NET 10's `dotnet new sln` produces the newer `.slnx`, which Godot ignores; without a `.sln` the export omits the C# assembly and produces a non-functional ~29 MB APK instead of a working ~105 MB one. Provide a hand-written classic `.sln`.
4. **ETC2/ASTC.** Set `rendering/textures/vram_compression/import_etc2_astc=true` in `project.godot`.
5. **JDK + keystore.** Editor setting `export/android/java_sdk_path` must point at the JDK (`/usr/lib/jvm/java-17-openjdk`). A debug keystore is needed at `~/.local/share/godot/keystores/debug.keystore` (alias `androiddebugkey`, store/key pass `android`); generate with `keytool` if missing.
6. **Headless export caveat.** Godot flags C#/.NET Android export as experimental. Headless export works, but one headless editor invocation crashed on shutdown (signal 11) without affecting the produced APK.

Confirmed result: hardware Vulkan (Forward Mobile) rendering, single-touch input, `Input.VibrateHandheld`, and `user://` save file read/write all worked on-device; ~259 MB PSS; startup under a second.

## Network Constraint

Respect the existing network and mirror configuration. Do not assume where `krfoss` is configured, and do not change package-manager, Fish, Kitty, OpenCode, provider, proxy, repository, or MCP settings merely to normalize the machine.

If a required source is unreachable, report:

- the requested host or package source
- why it is required
- the observed failure
- whether an approved mirror or offline method exists

Do not repeatedly retry, bypass restrictions, or select a new mirror without approval.

## Updating This Document

After the development-PC audit:

1. Replace assumptions with confirmed values where evidence exists.
2. Include the date and method of validation.
3. Preserve unresolved items as explicit unknowns.
4. Do not record secrets, personal tokens, or unnecessary machine identifiers.
5. Move durable technology choices into decision records rather than burying them here.
