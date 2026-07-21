# Boat Steering Slice Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the first playable slice — a placeholder box-boat that moves and steers on a flat sea plane, followed by a smoothed chase camera, controllable by keyboard and touch, running on desktop and on the Android device.

**Architecture:** A plain C# simulation core (`Astral.Sim.BoatMotion`) in a standalone class library holds all authoritative movement math with zero Godot dependency and is unit-tested in isolation. A thin Godot boat node feeds it input and applies its state to the scene. Camera, wake, heel, and a dev-only debug overlay only read simulation state; nothing in presentation computes movement rules.

**Tech Stack:** Godot 4.7 `.mono` (`godot-mono`), .NET SDK 10.0.110 targeting `net9.0`, C#, xUnit for tests. Godot built-in primitive meshes only. No Blender, no new runtime dependencies.

## Global Constraints

Copied from `docs/development/decisions/001-godot-csharp-android.md` and the design spec. Every task's requirements implicitly include these.

- Engine: `godot-mono` 4.7.stable.mono, binary `/usr/bin/godot-mono`. Never use plain `godot` for this C# project.
- All C# `.csproj` files target `<TargetFramework>net9.0</TargetFramework>`. Not net8, not net10.
- A classic-format `.sln` (not `.slnx`) must exist at the project root; `dotnet new sln` on .NET 10 produces `.slnx` and must not be used to create the Godot solution — write the `.sln` by hand or reuse the validated template in `environment.md`.
- Android export requires `rendering/textures/vram_compression/import_etc2_astc=true` in `project.godot`.
- Android export preset targets `arm64-v8a` only; uses the debug keystore at `~/.local/share/godot/keystores/debug.keystore` (alias `androiddebugkey`, store/key pass `android`); `.mono` export templates are installed at `~/.local/share/godot/export_templates/4.7.stable.mono/`.
- Editor setting `export/android/java_sdk_path` = `/usr/lib/jvm/java-17-openjdk` (already set).
- The simulation core (`Astral.Sim`) must not reference `Godot` or any Godot type. Presentation code must never contain authoritative movement rules (AGENTS.md).
- The test framework is a development-only dependency and must not be referenced by the Godot game assembly or shipped in the APK.
- No numeric HUD in the normal view; speed/heading numbers appear only in a debug overlay that is hidden by default.
- Named tuning constants only; no unexplained magic numbers in movement code.
- Do not commit, push, or publish unless explicitly requested by the owner. (The per-task "Commit" steps stage and commit locally only; that is the owner's established expectation for plan execution. Do not push.)

**Repository layout produced by this plan:**

```
/ (repo root)
├── project.godot                  # Godot project (root)
├── Astral.sln                  # classic-format solution
├── Astral.Game.csproj          # Godot game assembly (net9.0)
├── icon.svg                       # project icon (avoids export warning)
├── export_presets.cfg             # Android arm64 debug preset
├── sim/
│   ├── Astral.Sim.csproj       # plain C# class library (net9.0, no Godot)
│   ├── BoatTuning.cs
│   ├── Vec2.cs
│   └── BoatMotion.cs
├── tests/
│   └── Astral.Sim.Tests/
│       ├── Astral.Sim.Tests.csproj   # xunit, references sim/
│       └── BoatMotionTests.cs
├── scripts/
│   ├── Boat.cs
│   ├── BoatInput.cs
│   └── ChaseCamera.cs
├── scenes/
│   └── Main.tscn
└── build/                         # APK output (gitignored)
```

---

### Task 1: Simulation math primitives and tuning (`Vec2`, `BoatTuning`)

Foundation types for the sim core. Pure C#, no Godot. Establishes the sim class library and the test project so later tasks have somewhere to add tests.

**Files:**
- Create: `sim/Astral.Sim.csproj`
- Create: `sim/Vec2.cs`
- Create: `sim/BoatTuning.cs`
- Create: `tests/Astral.Sim.Tests/Astral.Sim.Tests.csproj`
- Create: `tests/Astral.Sim.Tests/BoatMotionTests.cs`
- Create: `Astral.sln`

**Interfaces:**
- Produces:
  - `namespace Astral.Sim; public readonly struct Vec2 { public double X {get;} public double Z {get;} public Vec2(double x, double z); public static Vec2 FromHeading(double headingRadians); }` — `FromHeading` returns a unit vector where heading 0 points +Z and increases toward +X (clockwise viewed from above).
  - `public struct BoatTuning { public double MaxForwardSpeed; public double ForwardAccel; public double LinearDrag; public double MaxTurnRate; public double TurnResponse; public double AngularDrag; public double SpeedForFullTurn; public static BoatTuning Default { get; } }`

- [ ] **Step 1: Create the sim class library project**

Create `sim/Astral.Sim.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net9.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <RootNamespace>Astral.Sim</RootNamespace>
  </PropertyGroup>
</Project>
```

- [ ] **Step 2: Create the test project**

Create `tests/Astral.Sim.Tests/Astral.Sim.Tests.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net9.0</TargetFramework>
    <Nullable>enable</Nullable>
    <IsPackable>false</IsPackable>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.NET.Test.Sdk" Version="17.11.1" />
    <PackageReference Include="xunit" Version="2.9.2" />
    <PackageReference Include="xunit.runner.visualstudio" Version="2.8.2" />
  </ItemGroup>
  <ItemGroup>
    <ProjectReference Include="../../sim/Astral.Sim.csproj" />
  </ItemGroup>
</Project>
```

- [ ] **Step 3: Create the classic-format solution by hand**

Create `Astral.sln` (classic format — do NOT use `dotnet new sln`). Use fixed GUIDs so the file is stable:

```
Microsoft Visual Studio Solution File, Format Version 12.00
# Visual Studio Version 17
VisualStudioVersion = 17.0.0.0
MinimumVisualStudioVersion = 10.0.40219.1
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "Astral.Sim", "sim\Astral.Sim.csproj", "{11111111-1111-1111-1111-111111111111}"
EndProject
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "Astral.Sim.Tests", "tests\Astral.Sim.Tests\Astral.Sim.Tests.csproj", "{22222222-2222-2222-2222-222222222222}"
EndProject
Global
	GlobalSection(SolutionConfigurationPlatforms) = preSolution
		Debug|Any CPU = Debug|Any CPU
		Release|Any CPU = Release|Any CPU
	EndGlobalSection
	GlobalSection(ProjectConfigurationPlatforms) = postSolution
		{11111111-1111-1111-1111-111111111111}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{11111111-1111-1111-1111-111111111111}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{11111111-1111-1111-1111-111111111111}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{11111111-1111-1111-1111-111111111111}.Release|Any CPU.Build.0 = Release|Any CPU
		{22222222-2222-2222-2222-222222222222}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{22222222-2222-2222-2222-222222222222}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{22222222-2222-2222-2222-222222222222}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{22222222-2222-2222-2222-222222222222}.Release|Any CPU.Build.0 = Release|Any CPU
	EndGlobalSection
EndGlobal
```

- [ ] **Step 4: Write `Vec2`**

Create `sim/Vec2.cs`:

```csharp
namespace Astral.Sim;

/// <summary>Planar position/direction on the water surface (X east, Z north).</summary>
public readonly struct Vec2
{
    public double X { get; }
    public double Z { get; }

    public Vec2(double x, double z)
    {
        X = x;
        Z = z;
    }

    /// <summary>Unit vector for a heading in radians. Heading 0 points +Z; increasing heading rotates toward +X.</summary>
    public static Vec2 FromHeading(double headingRadians)
        => new(System.Math.Sin(headingRadians), System.Math.Cos(headingRadians));

    public static Vec2 operator +(Vec2 a, Vec2 b) => new(a.X + b.X, a.Z + b.Z);
    public static Vec2 operator *(Vec2 a, double s) => new(a.X * s, a.Z * s);
}
```

- [ ] **Step 5: Write `BoatTuning`**

Create `sim/BoatTuning.cs`:

```csharp
namespace Astral.Sim;

/// <summary>Named tuning values for boat movement. All units are per second unless noted.</summary>
public struct BoatTuning
{
    /// <summary>Maximum forward speed in world units/second.</summary>
    public double MaxForwardSpeed;
    /// <summary>Forward acceleration applied at full throttle (units/second^2).</summary>
    public double ForwardAccel;
    /// <summary>Fraction of speed bled off per second when coasting (0..n).</summary>
    public double LinearDrag;
    /// <summary>Maximum turn rate in radians/second at full effectiveness.</summary>
    public double MaxTurnRate;
    /// <summary>How quickly steering input builds angular velocity (radians/second^2 at full steer).</summary>
    public double TurnResponse;
    /// <summary>Fraction of angular velocity bled off per second.</summary>
    public double AngularDrag;
    /// <summary>Speed at which turning reaches full effectiveness; below this, turning is proportionally weaker (momentum rule).</summary>
    public double SpeedForFullTurn;

    public static BoatTuning Default => new()
    {
        MaxForwardSpeed = 8.0,
        ForwardAccel = 4.0,
        LinearDrag = 0.6,
        MaxTurnRate = 1.2,
        TurnResponse = 3.0,
        AngularDrag = 2.0,
        SpeedForFullTurn = 3.0,
    };
}
```

- [ ] **Step 6: Add a placeholder test file so the test project compiles**

Create `tests/Astral.Sim.Tests/BoatMotionTests.cs`:

```csharp
using Astral.Sim;
using Xunit;

public class BoatMotionTests
{
    [Fact]
    public void Vec2_FromHeading_Zero_PointsAlongPositiveZ()
    {
        var v = Vec2.FromHeading(0.0);
        Assert.Equal(0.0, v.X, 6);
        Assert.Equal(1.0, v.Z, 6);
    }

    [Fact]
    public void BoatTuning_Default_HasPositiveMaxSpeed()
    {
        Assert.True(BoatTuning.Default.MaxForwardSpeed > 0);
    }
}
```

- [ ] **Step 7: Run the tests to verify the toolchain and these types**

Run: `dotnet test tests/Astral.Sim.Tests/Astral.Sim.Tests.csproj`
Expected: build succeeds, 2 tests PASS.

- [ ] **Step 8: Add a root .gitignore for build artifacts**

Create `.gitignore` at repo root (append if it exists):

```
# .NET / Godot build artifacts
bin/
obj/
build/
.godot/
*.user
```

- [ ] **Step 9: Commit**

```bash
git add sim/ tests/ Astral.sln .gitignore
git commit -m "feat(sim): add Vec2 and BoatTuning primitives with test project"
```

---

### Task 2: `BoatMotion` forward throttle and coasting

Implement the linear (speed) half of the sim with TDD.

**Files:**
- Create: `sim/BoatMotion.cs`
- Modify: `tests/Astral.Sim.Tests/BoatMotionTests.cs`

**Interfaces:**
- Consumes: `Vec2`, `BoatTuning` from Task 1.
- Produces:
  - `public sealed class BoatMotion { public BoatMotion(BoatTuning tuning); public Vec2 Position { get; } public double Heading { get; } public double Speed { get; } public double TurnRate { get; } public void Step(double throttleIntent, double steerIntent, double dt); }`
  - In this task `Step` implements throttle + drag + position integration; steering is added in Task 3 (leave `steerIntent` accepted but wire turning in Task 3).

- [ ] **Step 1: Write failing tests for throttle and coasting**

Replace the contents of `tests/Astral.Sim.Tests/BoatMotionTests.cs` with:

```csharp
using Astral.Sim;
using Xunit;

public class BoatMotionTests
{
    private static BoatMotion NewBoat() => new(BoatTuning.Default);

    [Fact]
    public void Vec2_FromHeading_Zero_PointsAlongPositiveZ()
    {
        var v = Vec2.FromHeading(0.0);
        Assert.Equal(0.0, v.X, 6);
        Assert.Equal(1.0, v.Z, 6);
    }

    [Fact]
    public void FullThrottle_IncreasesSpeed()
    {
        var boat = NewBoat();
        boat.Step(1.0, 0.0, 0.1);
        Assert.True(boat.Speed > 0.0);
    }

    [Fact]
    public void FullThrottle_DoesNotExceedMaxSpeed()
    {
        var boat = NewBoat();
        for (int i = 0; i < 1000; i++) boat.Step(1.0, 0.0, 0.1);
        Assert.True(boat.Speed <= BoatTuning.Default.MaxForwardSpeed + 1e-6);
    }

    [Fact]
    public void ZeroInput_CausesCoastingDownTowardZero()
    {
        var boat = NewBoat();
        for (int i = 0; i < 50; i++) boat.Step(1.0, 0.0, 0.1); // build speed
        double moving = boat.Speed;
        for (int i = 0; i < 200; i++) boat.Step(0.0, 0.0, 0.1); // release
        Assert.True(boat.Speed < moving);
        Assert.True(boat.Speed >= 0.0);
    }

    [Fact]
    public void Position_AdvancesAlongHeading()
    {
        var boat = NewBoat();
        for (int i = 0; i < 20; i++) boat.Step(1.0, 0.0, 0.1);
        // heading 0 -> moves +Z, X stays ~0
        Assert.True(boat.Position.Z > 0.0);
        Assert.Equal(0.0, boat.Position.X, 6);
    }

    [Fact]
    public void Step_IgnoresNonPositiveDt()
    {
        var boat = NewBoat();
        boat.Step(1.0, 0.0, 0.0);
        boat.Step(1.0, 0.0, -1.0);
        Assert.Equal(0.0, boat.Speed, 6);
        Assert.Equal(0.0, boat.Position.Z, 6);
    }

    [Fact]
    public void Step_ClampsThrottleIntent()
    {
        var a = NewBoat();
        var b = NewBoat();
        for (int i = 0; i < 10; i++) { a.Step(5.0, 0.0, 0.1); b.Step(1.0, 0.0, 0.1); }
        Assert.Equal(b.Speed, a.Speed, 6); // 5.0 clamped to 1.0
    }
}
```

- [ ] **Step 2: Run tests to verify they fail**

Run: `dotnet test tests/Astral.Sim.Tests/Astral.Sim.Tests.csproj`
Expected: FAIL — `BoatMotion` does not exist (compile error).

- [ ] **Step 3: Implement `BoatMotion` (throttle + drag + position; steering stubbed)**

Create `sim/BoatMotion.cs`:

```csharp
namespace Astral.Sim;

/// <summary>
/// Authoritative, Godot-free boat movement. Coordinates are planar (X east, Z north);
/// heading 0 points +Z and increases toward +X.
/// </summary>
public sealed class BoatMotion
{
    private readonly BoatTuning _t;

    public Vec2 Position { get; private set; }
    public double Heading { get; private set; }
    public double Speed { get; private set; }
    public double TurnRate { get; private set; }

    public BoatMotion(BoatTuning tuning)
    {
        _t = tuning;
    }

    public void Step(double throttleIntent, double steerIntent, double dt)
    {
        if (dt <= 0.0) return;

        throttleIntent = Clamp(throttleIntent, -1.0, 1.0);
        steerIntent = Clamp(steerIntent, -1.0, 1.0);

        // Linear speed: accelerate toward throttle target, then apply drag.
        Speed += throttleIntent * _t.ForwardAccel * dt;
        Speed -= Speed * _t.LinearDrag * dt;
        Speed = Clamp(Speed, 0.0, _t.MaxForwardSpeed);

        // Steering is added in Task 3. TurnRate stays 0 here.

        // Integrate position along heading.
        Position += Vec2.FromHeading(Heading) * (Speed * dt);
    }

    private static double Clamp(double v, double lo, double hi)
        => v < lo ? lo : (v > hi ? hi : v);
}
```

- [ ] **Step 4: Run tests to verify they pass**

Run: `dotnet test tests/Astral.Sim.Tests/Astral.Sim.Tests.csproj`
Expected: all tests PASS.

- [ ] **Step 5: Commit**

```bash
git add sim/BoatMotion.cs tests/Astral.Sim.Tests/BoatMotionTests.cs
git commit -m "feat(sim): BoatMotion throttle, drag, and position integration"
```

---

### Task 3: `BoatMotion` momentum-preserving steering

Add turning that scales with speed (a near-stationary boat barely turns), with angular drag.

**Files:**
- Modify: `sim/BoatMotion.cs`
- Modify: `tests/Astral.Sim.Tests/BoatMotionTests.cs`

**Interfaces:**
- Consumes: `BoatMotion` from Task 2.
- Produces: same `BoatMotion` public surface; `Step` now updates `TurnRate` and `Heading` from `steerIntent`, scaled by current speed relative to `SpeedForFullTurn`.

- [ ] **Step 1: Add failing steering tests**

Append these tests inside the `BoatMotionTests` class in `tests/Astral.Sim.Tests/BoatMotionTests.cs`:

```csharp
    [Fact]
    public void SteeringAtSpeed_ChangesHeading()
    {
        var boat = NewBoat();
        for (int i = 0; i < 50; i++) boat.Step(1.0, 0.0, 0.1); // reach speed
        double before = boat.Heading;
        for (int i = 0; i < 20; i++) boat.Step(1.0, 1.0, 0.1);  // steer
        Assert.NotEqual(before, boat.Heading);
    }

    [Fact]
    public void SteeringWhileNearlyStationary_BarelyChangesHeading()
    {
        var moving = NewBoat();
        for (int i = 0; i < 50; i++) moving.Step(1.0, 0.0, 0.1);
        double movingBefore = moving.Heading;
        for (int i = 0; i < 10; i++) moving.Step(1.0, 1.0, 0.1);
        double movingDelta = System.Math.Abs(moving.Heading - movingBefore);

        var still = NewBoat();
        double stillBefore = still.Heading;
        for (int i = 0; i < 10; i++) still.Step(0.0, 1.0, 0.1); // no throttle -> ~no speed
        double stillDelta = System.Math.Abs(still.Heading - stillBefore);

        Assert.True(stillDelta < movingDelta,
            $"stationary turn delta {stillDelta} should be less than moving {movingDelta}");
    }

    [Fact]
    public void ReleasingSteer_AngularVelocityDecays()
    {
        var boat = NewBoat();
        for (int i = 0; i < 50; i++) boat.Step(1.0, 0.0, 0.1);
        for (int i = 0; i < 10; i++) boat.Step(1.0, 1.0, 0.1); // spin up turn
        double turning = System.Math.Abs(boat.TurnRate);
        for (int i = 0; i < 50; i++) boat.Step(1.0, 0.0, 0.1); // release steer
        Assert.True(System.Math.Abs(boat.TurnRate) < turning);
    }
```

- [ ] **Step 2: Run tests to verify the new ones fail**

Run: `dotnet test tests/Astral.Sim.Tests/Astral.Sim.Tests.csproj`
Expected: the three new tests FAIL (heading never changes; TurnRate stays 0).

- [ ] **Step 3: Implement steering in `Step`**

In `sim/BoatMotion.cs`, replace the comment line `// Steering is added in Task 3. TurnRate stays 0 here.` and integrate turning. The `Step` method body becomes:

```csharp
    public void Step(double throttleIntent, double steerIntent, double dt)
    {
        if (dt <= 0.0) return;

        throttleIntent = Clamp(throttleIntent, -1.0, 1.0);
        steerIntent = Clamp(steerIntent, -1.0, 1.0);

        // Linear speed: accelerate toward throttle target, then apply drag.
        Speed += throttleIntent * _t.ForwardAccel * dt;
        Speed -= Speed * _t.LinearDrag * dt;
        Speed = Clamp(Speed, 0.0, _t.MaxForwardSpeed);

        // Turning effectiveness scales with how close we are to SpeedForFullTurn.
        double effectiveness = _t.SpeedForFullTurn <= 0.0
            ? 1.0
            : Clamp(Speed / _t.SpeedForFullTurn, 0.0, 1.0);

        TurnRate += steerIntent * _t.TurnResponse * effectiveness * dt;
        TurnRate -= TurnRate * _t.AngularDrag * dt;
        TurnRate = Clamp(TurnRate, -_t.MaxTurnRate, _t.MaxTurnRate);

        Heading += TurnRate * dt;

        // Integrate position along heading.
        Position += Vec2.FromHeading(Heading) * (Speed * dt);
    }
```

- [ ] **Step 4: Run tests to verify all pass**

Run: `dotnet test tests/Astral.Sim.Tests/Astral.Sim.Tests.csproj`
Expected: all tests PASS.

- [ ] **Step 5: Commit**

```bash
git add sim/BoatMotion.cs tests/Astral.Sim.Tests/BoatMotionTests.cs
git commit -m "feat(sim): momentum-preserving steering with angular drag"
```

---

### Task 4: Godot project scaffold that references the sim library

Create the root Godot `.mono` project, game assembly, solution wiring, icon, and project settings so `godot-mono` opens and builds with the sim library referenced. No gameplay yet — this task's deliverable is "the editor builds the C# and the sim types are reachable."

**Files:**
- Create: `project.godot`
- Create: `Astral.Game.csproj`
- Create: `icon.svg`
- Modify: `Astral.sln`
- Create: `scripts/BuildProbe.cs`
- Create: `scenes/Main.tscn`

**Interfaces:**
- Consumes: `Astral.Sim` class library (Task 1–3).
- Produces: a Godot project named `Astral` whose main scene is `res://scenes/Main.tscn`; game assembly `Astral.Game` referencing `sim/Astral.Sim.csproj`.

- [ ] **Step 1: Create the Godot game assembly project**

Create `Astral.Game.csproj`:

```xml
<Project Sdk="Godot.NET.Sdk/4.7.0">
  <PropertyGroup>
    <TargetFramework>net9.0</TargetFramework>
    <EnableDynamicLoading>true</EnableDynamicLoading>
    <Nullable>enable</Nullable>
  </PropertyGroup>
  <ItemGroup>
    <ProjectReference Include="sim/Astral.Sim.csproj" />
  </ItemGroup>
</Project>
```

- [ ] **Step 2: Add the game project to the solution**

Edit `Astral.sln`: add the game project entry after the existing `Project(...)` blocks (before `Global`), and add its configuration lines. Insert this project line:

```
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "Astral.Game", "Astral.Game.csproj", "{33333333-3333-3333-3333-333333333333}"
EndProject
```

And add these lines inside `GlobalSection(ProjectConfigurationPlatforms) = postSolution`:

```
		{33333333-3333-3333-3333-333333333333}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{33333333-3333-3333-3333-333333333333}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{33333333-3333-3333-3333-333333333333}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{33333333-3333-3333-3333-333333333333}.Release|Any CPU.Build.0 = Release|Any CPU
```

- [ ] **Step 3: Create a project icon to avoid export warnings**

Create `icon.svg` (a minimal valid SVG):

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="128" height="128" viewBox="0 0 128 128"><rect width="128" height="128" fill="#1b4b6b"/><path d="M24 92 L64 40 L104 92 Z" fill="#dfe9ef"/></svg>
```

- [ ] **Step 4: Create `project.godot`**

Create `project.godot`:

```
; Astral — boat steering slice
config_version=5

[application]

config/name="Astral"
config/features=PackedStringArray("4.7", "C#", "Forward Plus")
run/main_scene="res://scenes/Main.tscn"

[dotnet]

project/assembly_name="Astral.Game"

[rendering]

textures/vram_compression/import_etc2_astc=true
```

- [ ] **Step 5: Add a temporary build-probe script that uses a sim type**

Create `scripts/BuildProbe.cs` (proves the game assembly can reference the sim library; removed in Task 5):

```csharp
using Godot;
using Astral.Sim;

public partial class BuildProbe : Node
{
    public override void _Ready()
    {
        var boat = new BoatMotion(BoatTuning.Default);
        boat.Step(1.0, 0.0, 0.1);
        GD.Print($"[PROBE] sim reachable, speed={boat.Speed:F3}");
    }
}
```

- [ ] **Step 6: Create a minimal main scene using the probe**

Create `scenes/Main.tscn`:

```
[gd_scene load_steps=2 format=3 uid="uid://celestialmain0"]

[ext_resource type="Script" path="res://scripts/BuildProbe.cs" id="1"]

[node name="Main" type="Node3D"]
script = ExtResource("1")
```

- [ ] **Step 7: Import and build headless, verify the sim is reachable**

Run:
```bash
godot-mono --headless --path . --import
dotnet build Astral.Game.csproj
godot-mono --headless --path . 2>&1 | grep PROBE
```
Expected: build succeeds; output contains `[PROBE] sim reachable, speed=0.400` (approx). The `--import` step may crash on shutdown (known headless bug per environment.md) — that is acceptable as long as the build and PROBE run succeed.

- [ ] **Step 8: Commit**

```bash
git add project.godot Astral.Game.csproj Astral.sln icon.svg scripts/BuildProbe.cs scenes/Main.tscn
git commit -m "feat(godot): scaffold Astral .mono project referencing sim library"
```

---

### Task 5: Shared input layer (`BoatInput`)

One helper that turns keyboard and touch into abstract `(throttleIntent, steerIntent)`. Register input actions in `project.godot`.

**Files:**
- Modify: `project.godot`
- Create: `scripts/BoatInput.cs`

**Interfaces:**
- Produces:
  - `public sealed class BoatInput { public void HandleEvent(InputEvent e, Vector2 viewportSize); public (float throttle, float steer) Poll(); }`
  - Keyboard is read in `Poll()` via `Input.GetActionStrength`; touch is accumulated in `HandleEvent` and read in `Poll()`. `throttle` and `steer` are each in [-1, 1].

- [ ] **Step 1: Add input actions to `project.godot`**

Append to `project.godot`:

```
[input]

boat_forward={
"deadzone": 0.5,
"events": [Object(InputEventKey,"physical_keycode":87)]
}
boat_back={
"deadzone": 0.5,
"events": [Object(InputEventKey,"physical_keycode":83)]
}
boat_left={
"deadzone": 0.5,
"events": [Object(InputEventKey,"physical_keycode":65)]
}
boat_right={
"deadzone": 0.5,
"events": [Object(InputEventKey,"physical_keycode":68)]
}
toggle_debug={
"deadzone": 0.5,
"events": [Object(InputEventKey,"physical_keycode":96)]
}
```

(Physical keycodes: W=87, S=83, A=65, D=68, KP_0=96.)

- [ ] **Step 2: Implement `BoatInput`**

Create `scripts/BoatInput.cs`:

```csharp
using Godot;

/// <summary>
/// Translates keyboard actions and a single-finger touch drag into abstract
/// (throttle, steer) intents in [-1, 1]. The simulation never learns which device was used.
/// </summary>
public sealed class BoatInput
{
    private bool _touchActive;
    private long _touchIndex = -1;
    private Vector2 _touchStart;
    private Vector2 _touchCurrent;

    // A drag of this many pixels maps to full intent.
    private const float DragRangePixels = 220f;

    public void HandleEvent(InputEvent e, Vector2 viewportSize)
    {
        switch (e)
        {
            case InputEventScreenTouch touch when touch.Pressed && !_touchActive:
                _touchActive = true;
                _touchIndex = touch.Index;
                _touchStart = touch.Position;
                _touchCurrent = touch.Position;
                break;
            case InputEventScreenTouch touch when !touch.Pressed && touch.Index == _touchIndex:
                _touchActive = false;
                _touchIndex = -1;
                break;
            case InputEventScreenDrag drag when _touchActive && drag.Index == _touchIndex:
                _touchCurrent = drag.Position;
                break;
        }
    }

    public (float throttle, float steer) Poll()
    {
        // Keyboard.
        float kbThrottle = Input.GetActionStrength("boat_forward") - Input.GetActionStrength("boat_back");
        float kbSteer = Input.GetActionStrength("boat_right") - Input.GetActionStrength("boat_left");

        // Touch: up = forward, right = steer right.
        float tThrottle = 0f, tSteer = 0f;
        if (_touchActive)
        {
            Vector2 d = _touchCurrent - _touchStart;
            tThrottle = Mathf.Clamp(-d.Y / DragRangePixels, -1f, 1f);
            tSteer = Mathf.Clamp(d.X / DragRangePixels, -1f, 1f);
        }

        float throttle = Mathf.Clamp(kbThrottle + tThrottle, -1f, 1f);
        float steer = Mathf.Clamp(kbSteer + tSteer, -1f, 1f);
        return (throttle, steer);
    }
}
```

- [ ] **Step 3: Verify it compiles**

Run: `dotnet build Astral.Game.csproj`
Expected: build succeeds, 0 errors.

- [ ] **Step 4: Commit**

```bash
git add project.godot scripts/BoatInput.cs
git commit -m "feat(input): shared keyboard+touch boat input layer"
```

---

### Task 6: `Boat` node — drive the sim and apply state to the scene

Replace the build probe with the real boat node that owns a `BoatMotion`, reads `BoatInput`, and moves the hull. Presentation heel is applied here (visual only).

**Files:**
- Create: `scripts/Boat.cs`
- Delete: `scripts/BuildProbe.cs`
- Modify: `scenes/Main.tscn`

**Interfaces:**
- Consumes: `BoatMotion`, `BoatTuning` (sim); `BoatInput` (Task 5).
- Produces:
  - `public partial class Boat : Node3D { public float Speed01 { get; } public float Heading { get; } public Vector3 SimVelocityDir { get; } }` — read-only accessors the camera/wake/debug read. `Speed01` is speed normalised to [0,1] by `MaxForwardSpeed`.

- [ ] **Step 1: Implement `Boat`**

Create `scripts/Boat.cs`:

```csharp
using Godot;
using Astral.Sim;

public partial class Boat : Node3D
{
    private const float WaterHeight = 0f;
    private const float MaxHeelDegrees = 12f;
    private const float HeelSmoothing = 6f;

    private readonly BoatInput _input = new();
    private BoatMotion _motion = null!;
    private BoatTuning _tuning = BoatTuning.Default;
    private float _heel;

    public float Speed01 => (float)(_motion.Speed / _tuning.MaxForwardSpeed);
    public float Heading => (float)_motion.Heading;

    public override void _Ready()
    {
        _motion = new BoatMotion(_tuning);
    }

    public override void _UnhandledInput(InputEvent @event)
    {
        _input.HandleEvent(@event, GetViewport().GetVisibleRect().Size);
    }

    public override void _PhysicsProcess(double delta)
    {
        var (throttle, steer) = _input.Poll();
        _motion.Step(throttle, steer, delta);

        // Apply authoritative sim state to the transform.
        Position = new Vector3((float)_motion.Position.X, WaterHeight, (float)_motion.Position.Z);

        // Presentation-only heel: lean into the turn, proportional to turn rate. Does not affect the sim.
        float targetHeel = Mathf.Clamp((float)(-_motion.TurnRate / _tuning.MaxTurnRate), -1f, 1f)
                           * Mathf.DegToRad(MaxHeelDegrees);
        _heel = Mathf.Lerp(_heel, targetHeel, (float)delta * HeelSmoothing);

        Rotation = new Vector3(0f, (float)_motion.Heading, _heel);
    }
}
```

- [ ] **Step 2: Build the Main scene with sea, boat, hull, light**

Replace `scenes/Main.tscn` with:

```
[gd_scene load_steps=6 format=3 uid="uid://celestialmain0"]

[ext_resource type="Script" path="res://scripts/Boat.cs" id="1"]

[sub_resource type="PlaneMesh" id="PlaneMesh_1"]
size = Vector2(400, 400)

[sub_resource type="StandardMaterial3D" id="Mat_sea"]
albedo_color = Color(0.09, 0.3, 0.45, 1)

[sub_resource type="BoxMesh" id="BoxMesh_hull"]
size = Vector3(1.2, 0.6, 3)

[sub_resource type="StandardMaterial3D" id="Mat_hull"]
albedo_color = Color(0.8, 0.7, 0.5, 1)

[node name="Main" type="Node3D"]

[node name="Sea" type="MeshInstance3D" parent="."]
mesh = SubResource("PlaneMesh_1")
surface_material_override/0 = SubResource("Mat_sea")

[node name="SunLight" type="DirectionalLight3D" parent="."]
transform = Transform3D(0.7, -0.5, 0.5, 0, 0.7, 0.7, -0.7, -0.5, 0.5, 0, 12, 0)

[node name="Boat" type="Node3D" parent="."]
script = ExtResource("1")

[node name="Hull" type="MeshInstance3D" parent="Boat"]
transform = Transform3D(1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0.3, 0)
mesh = SubResource("BoxMesh_hull")
surface_material_override/0 = SubResource("Mat_hull")

[node name="Bow" type="MeshInstance3D" parent="Boat"]
transform = Transform3D(1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0.3, 1.7)
mesh = SubResource("BoxMesh_hull")
visible = true
```

Note: the "Bow" reuses the hull box scaled by its transform position to mark the front; the implementer may shrink it via a separate small BoxMesh if preferred, but a distinct forward marker must be visible.

- [ ] **Step 3: Delete the build probe**

```bash
git rm scripts/BuildProbe.cs
```

- [ ] **Step 4: Build and run on desktop briefly**

Run:
```bash
dotnet build Astral.Game.csproj
timeout 8 godot-mono --path . 2>&1 | grep -iE 'error|exception' || echo "no runtime errors"
```
Expected: builds; no C# exceptions on load. (Visual/feel is judged manually by the owner separately.)

- [ ] **Step 5: Commit**

```bash
git add scripts/Boat.cs scenes/Main.tscn
git commit -m "feat(boat): Boat node drives sim and applies state with heel"
```

---

### Task 7: `ChaseCamera` — smoothed elevated follow

A camera rig that eases toward an offset behind/above the boat and eases its look toward the boat, with separate position and rotation smoothing.

**Files:**
- Create: `scripts/ChaseCamera.cs`
- Modify: `scenes/Main.tscn`

**Interfaces:**
- Consumes: `Boat` (Task 6) via an exported `NodePath`.
- Produces: `public partial class ChaseCamera : Camera3D { }` following the target each `_Process`.

- [ ] **Step 1: Implement `ChaseCamera`**

Create `scripts/ChaseCamera.cs`:

```csharp
using Godot;

public partial class ChaseCamera : Camera3D
{
    [Export] public NodePath TargetPath { get; set; } = null!;

    // Offset in the boat's local space: behind (-Z) and above (+Y).
    private static readonly Vector3 FollowOffset = new(0f, 6f, -10f);
    private const float PositionSmoothing = 3.5f;
    private const float RotationSmoothing = 2.5f;

    private Node3D _target = null!;

    public override void _Ready()
    {
        _target = GetNode<Node3D>(TargetPath);
    }

    public override void _Process(double delta)
    {
        if (_target == null) return;

        // Desired position: offset rotated by the boat's heading only (ignore heel/pitch).
        float heading = _target.Rotation.Y;
        var basisY = new Basis(Vector3.Up, heading);
        Vector3 desiredPos = _target.GlobalPosition + basisY * FollowOffset;

        GlobalPosition = GlobalPosition.Lerp(desiredPos, (float)delta * PositionSmoothing);

        // Ease look direction toward the boat.
        Vector3 lookTarget = _target.GlobalPosition + Vector3.Up * 0.5f;
        Transform3D desired = GlobalTransform.LookingAt(lookTarget, Vector3.Up);
        Quaternion current = GlobalTransform.Basis.GetRotationQuaternion();
        Quaternion target = desired.Basis.GetRotationQuaternion();
        Quaternion smooth = current.Slerp(target, (float)delta * RotationSmoothing);
        GlobalTransform = new Transform3D(new Basis(smooth), GlobalPosition);
    }
}
```

- [ ] **Step 2: Add the camera to Main.tscn**

In `scenes/Main.tscn`: add the script to `load_steps` ext_resources and add a `ChaseCamera` node. Add this ext_resource line near the top (increment `load_steps` by 1):

```
[ext_resource type="Script" path="res://scripts/ChaseCamera.cs" id="2"]
```

And add this node at the end of the file:

```
[node name="ChaseCamera" type="Camera3D" parent="."]
transform = Transform3D(1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 6, -10)
script = ExtResource("2")
TargetPath = NodePath("../Boat")
```

- [ ] **Step 3: Build and run briefly**

Run:
```bash
dotnet build Astral.Game.csproj
timeout 8 godot-mono --path . 2>&1 | grep -iE 'error|exception' || echo "no runtime errors"
```
Expected: builds; no exceptions; camera exists.

- [ ] **Step 4: Commit**

```bash
git add scripts/ChaseCamera.cs scenes/Main.tscn
git commit -m "feat(camera): smoothed elevated chase camera"
```

---

### Task 8: Wake particles and dev debug overlay

Add the speed-driven wake trail and the hideable debug overlay (speed/heading). No numeric HUD is visible by default.

**Files:**
- Create: `scripts/DebugOverlay.cs`
- Modify: `scripts/Boat.cs`
- Modify: `scenes/Main.tscn`

**Interfaces:**
- Consumes: `Boat` accessors `Speed01`, `Heading` (Task 6).
- Produces: `public partial class DebugOverlay : CanvasLayer { [Export] public NodePath BoatPath; }` toggled by the `toggle_debug` action; hidden on start.

- [ ] **Step 1: Add a wake `CPUParticles3D` to the Boat in Main.tscn**

Use `CPUParticles3D` for Android reliability (per the design's fallback note). Add under the `Boat` node in `scenes/Main.tscn`:

```
[node name="Wake" type="CPUParticles3D" parent="Boat"]
transform = Transform3D(1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0.1, -1.6)
emitting = true
amount = 24
lifetime = 1.2
direction = Vector3(0, 0, -1)
initial_velocity_min = 0.5
initial_velocity_max = 1.0
gravity = Vector3(0, 0, 0)
scale_amount_min = 0.15
scale_amount_max = 0.35
```

- [ ] **Step 2: Drive wake emission from speed in `Boat.cs`**

In `scripts/Boat.cs`, add a field and wire it in `_Ready`/`_PhysicsProcess`. Add near the other fields:

```csharp
    private CpuParticles3D _wake = null!;
```

In `_Ready`, after `_motion = new BoatMotion(_tuning);` add:

```csharp
        _wake = GetNode<CpuParticles3D>("Wake");
```

At the end of `_PhysicsProcess`, add:

```csharp
        _wake.Emitting = Speed01 > 0.05f;
```

- [ ] **Step 3: Implement `DebugOverlay`**

Create `scripts/DebugOverlay.cs`:

```csharp
using Godot;

public partial class DebugOverlay : CanvasLayer
{
    [Export] public NodePath BoatPath { get; set; } = null!;

    private Boat _boat = null!;
    private Label _label = null!;

    public override void _Ready()
    {
        _boat = GetNode<Boat>(BoatPath);
        _label = GetNode<Label>("Label");
        Visible = false; // hidden by default; no HUD in the normal view
    }

    public override void _Process(double delta)
    {
        if (Input.IsActionJustPressed("toggle_debug"))
            Visible = !Visible;

        if (Visible)
            _label.Text = $"speed01: {_boat.Speed01:F2}\nheading: {Mathf.RadToDeg(_boat.Heading):F0} deg";
    }
}
```

- [ ] **Step 4: Add the DebugLayer to Main.tscn**

Add the ext_resource (increment `load_steps`):

```
[ext_resource type="Script" path="res://scripts/DebugOverlay.cs" id="3"]
```

Add at the end of the file:

```
[node name="DebugLayer" type="CanvasLayer" parent="."]
script = ExtResource("3")
BoatPath = NodePath("../Boat")

[node name="Label" type="Label" parent="DebugLayer"]
offset_left = 16.0
offset_top = 16.0
text = ""
```

- [ ] **Step 5: Build and run briefly**

Run:
```bash
dotnet build Astral.Game.csproj
timeout 8 godot-mono --path . 2>&1 | grep -iE 'error|exception' || echo "no runtime errors"
```
Expected: builds; no exceptions; overlay hidden by default.

- [ ] **Step 6: Commit**

```bash
git add scripts/DebugOverlay.cs scripts/Boat.cs scenes/Main.tscn
git commit -m "feat(feedback): speed-driven wake and toggleable debug overlay"
```

---

### Task 9: Android export preset and on-device run

Add the Android export preset and produce a signed debug APK that installs and runs on the device with touch steering.

**Files:**
- Create: `export_presets.cfg`

**Interfaces:**
- Consumes: the full project (Tasks 4–8).
- Produces: `build/celestial.apk` installable on arm64-v8a.

- [ ] **Step 1: Create `export_presets.cfg`**

Create `export_presets.cfg` (arm64-only debug preset, matching the validated settings in `environment.md`):

```
[preset.0]

name="Android"
platform="Android"
runnable=true
advanced_options=false
dedicated_server=false
custom_features=""
export_filter="all_resources"
include_filter=""
exclude_filter=""
export_path="build/celestial.apk"
patches=PackedStringArray()
encryption_include_filters=""
encryption_exclude_filters=""
seed=0
encrypt_pck=false
encrypt_directory=false
script_export_mode=2

[preset.0.options]

custom_template/debug=""
custom_template/release=""
gradle_build/use_gradle_build=false
architectures/armeabi-v7a=false
architectures/arm64-v8a=true
architectures/x86=false
architectures/x86_64=false
version/code=1
version/name="0.1"
package/unique_name="com.celestial.game"
package/name="Astral"
package/signed=true
package/app_category=2
screen/immersive_mode=true
screen/support_small=true
screen/support_normal=true
screen/support_large=true
screen/support_xlarge=true
permissions/internet=false
```

- [ ] **Step 2: Ensure the app is built for export**

Run: `dotnet build Astral.Game.csproj -c ExportDebug`
Expected: build succeeds (ExportDebug is the config Godot uses for debug exports).

- [ ] **Step 3: Export the debug APK headless**

Run:
```bash
mkdir -p build
godot-mono --headless --path . --export-debug "Android" build/celestial.apk 2>&1 | grep -iE 'Signed|error|no solution'
ls -la build/celestial.apk
```
Expected: `Signed` appears; `build/celestial.apk` exists and is roughly 100+ MB (C# runtime bundled — a ~30 MB APK means the C# assembly was omitted; if so, confirm `Astral.sln` includes the game project and re-export).

- [ ] **Step 4: Install and launch on the device (if connected)**

Run:
```bash
adb devices
adb install -r build/celestial.apk
adb logcat -c
adb shell monkey -p com.celestial.game -c android.intent.category.LAUNCHER 1
sleep 5
adb logcat -d | grep -iE 'godot|Astral|FATAL' | tail -15
```
Expected: `Success` on install; the app launches; logcat shows Godot starting with no FATAL. If no device is connected, record that on-device run is deferred and stop after Step 3.

- [ ] **Step 5: Commit**

```bash
git add export_presets.cfg
git commit -m "feat(android): arm64 debug export preset for on-device run"
```

---

### Task 10: Slice validation and handoff

Confirm the whole slice against the spec's success criteria and record results. No code changes; this task produces a validation record and a handoff.

**Files:**
- Create: `docs/handoffs/YYYY-MM-DD-boat-steering-slice.md` (use `docs/handoffs/template.md`)

- [ ] **Step 1: Run the full test suite**

Run: `dotnet test`
Expected: all `BoatMotion` tests PASS.

- [ ] **Step 2: Desktop feel check (owner-observed)**

Run: `godot-mono --path .`
Confirm, and record in the handoff: momentum-preserving steering; camera eases without snapping; wake appears at speed; heel visible in turns; `toggle_debug` shows/hides numbers.

- [ ] **Step 3: On-device check (if device connected)**

Confirm touch-drag steers the boat, camera is comfortable, frame pacing acceptable. Record device model and observations. Uninstall the disposable app afterward: `adb uninstall com.celestial.game`.

- [ ] **Step 4: Write the handoff**

Fill `docs/handoffs/YYYY-MM-DD-boat-steering-slice.md` from the template: what was built, what validated, what is deferred (waves, wind, sail — the next slice), and the answer to "is the steering/camera foundation comfortable?" Do not push or publish.

- [ ] **Step 5: Commit**

```bash
git add docs/handoffs/
git commit -m "docs: boat steering slice validation and handoff"
```

---

## Notes for the Implementer

- Godot `.tscn` and `project.godot` text formats are picky. If the editor rewrites a scene file on first open, prefer letting `godot-mono` regenerate it over hand-fighting the format — but keep node names and scripts as specified.
- The headless `--import` step can crash on shutdown (signal 11) without harming output; this is documented in `environment.md`. Judge success by the build and the expected printout, not by the exit code of `--import`.
- Never run plain `godot` against this project; always `godot-mono`.
- If a ~30 MB APK is produced instead of ~100 MB, the C# assembly was omitted — check that `Astral.sln` exists in classic format and lists `Astral.Game`, and that the `.csproj` targets `net9.0`.
