# Development Environment

## Purpose

This document records the intended development environment and separates owner-provided facts from assumptions requiring confirmation. It is not an installation script and does not authorize changes to any machine.

## Environment Status

The initial version of this document was written on a restricted Windows planning PC. No claim below has been validated on the CachyOS development PC unless its status explicitly says **Confirmed on development PC**.

## Owner-Provided Facts

| Item | Current status | Notes |
| --- | --- | --- |
| Primary development OS | Owner-reported | CachyOS, an Arch Linux-based distribution |
| Game engine | Owner-reported installed | Godot 4; exact version and edition unknown |
| 3D content tool | Owner-reported installed | Blender; exact version unknown |
| Primary agent interface | Owner decision | OpenCode |
| Godot MCP assistance | Owner-reported installed | Beckett MCP, purchased from itch.io |
| Blender MCP assistance | Owner-reported installed | BlenderMCP |
| Python | Assumed installed | Version and role unknown |
| `uv` | Assumed installed | Version and configuration unknown |
| Product platform | Owner decision | Android only |
| Distribution | Owner intention | Initially private for friends and family; possible free/nonprofit Play Store release |
| Reachable download mirror | Owner-reported constraint | `krfoss` is reportedly the only accessible mirror |
| Shell and terminal | Owner-reported | Fish and Kitty |

## Provisional Technical Direction

| Item | Status | Validation required |
| --- | --- | --- |
| Engine | Provisional | Confirm exact Godot 4 version and Android support |
| Language | Provisional | C# using Godot .NET |
| .NET SDK | Unknown | Confirm whether installed and compatible with the exact Godot version |
| Godot edition | Unknown | Confirm standard versus .NET-enabled build |
| Android toolchain | Unknown | Confirm export templates, JDK, SDK, build tools, and any required NDK components |
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
