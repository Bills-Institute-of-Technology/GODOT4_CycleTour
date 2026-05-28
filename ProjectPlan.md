# Project Plan — GODOT4 Cycle Tour

A phased development plan for a 3D bicycling simulation built with Godot 4 and C#. The plan is structured for incremental delivery, prioritizing early validation of core gameplay mechanics before expanding scope to terrain, assets, and game loop systems.

All feature work is managed through **AI OpenSpec** (spec-driven development). Before implementing any phase, the corresponding feature should have an OpenSpec proposal and specification in `openspec/`.

---

# Phase 0 — Environment Setup

## 0.1 Install Godot 4

- Install **Godot 4.x (stable, .NET build)** from [godotengine.org](https://godotengine.org/download).
- The .NET build is required for C# support.

## 0.2 Configure the IDE

Install the following extensions in VSCode / Windsurf:

**Required:**
- **Godot Tools** — GDScript/C# language server, autocomplete, jump-to-definition.
- **C# Dev Kit** — Full C# IntelliSense, project management, and debugging support.

**Recommended:**
- **GitLens** — Enhanced Git history and blame annotations.
- **Blender** (standalone tool) — Mesh editing, terrain sculpting, and cyclist model rigging.

## 0.3 Godot Project Settings for 3D

Apply the following settings when creating the project:

- **Renderer:** Forward+ (default; required for high-quality 3D lighting and shadows)
- **Display -> Window -> Stretch Mode:** `canvas_items` (correct UI scaling across resolutions)
- **Physics -> 3D -> Solver Iterations:** `16` (improved stability for physics-driven character movement)
- **Editor -> Interface -> Single Window Mode:** Enabled (recommended when editing externally in VSCode/Windsurf)

---

# Phase 1 — Project Structure

Establish the following folder structure before writing any code. Consistency here prevents scene and asset management issues as the project scales.

```
/scenes
    /player
    /environment
    /ui
/scripts
/assets
    /models
    /textures
    /animations
/openspec
    /changes
    /specs
```

---

# Phase 2 — Development Order Rationale

The cyclist controller and core physics must be implemented and tuned **before** terrain or stage content is built. This order is intentional:

- Terrain geometry is not meaningful until slope response, turning radius, speed behavior, and camera feel are validated.
- Physics tuning requires a controlled, minimal environment — a large terrain introduces unnecessary variables.
- A stable controller makes terrain a testing asset rather than a debugging liability.

This mirrors the prototyping workflow used in commercial racing and vehicle simulation games.

---

# Phase 3 — Cyclist Prototype (Core Controller)

## 3.1 Create a Minimal Test Scene

Create `TestWorld.tscn` with:
- `DirectionalLight3D`
- `Camera3D`
- `PlaneMesh` (ground plane)
- `CharacterBody3D` named `PlayerBike`

## 3.2 Add Placeholder Geometry

Use primitive meshes to represent the cyclist before importing a final model:
- `CapsuleMesh` — rider body
- `CylinderMesh` — wheels
- `CubeMesh` — handlebars

This approach eliminates early dependencies on external assets or 3D modeling tools.

## 3.3 Implement Core Movement (C# Script)

Implement the following in a C# script attached to `PlayerBike`:

- Forward/backward acceleration
- Left/right steering
- Gravity and slope detection
- Speed limits (flat, climb, and descent variants)
- Acceleration and deceleration curves
- Slope-proportional speed adjustment

## 3.4 Add Chase Camera

Attach a chase camera using:
- `SpringArm3D` with collision enabled
- `Camera3D` as child node

Tune the following parameters:
- Follow lag (smoothing delay)
- Camera height and follow distance
- Rotation smoothing
- Behavior on steep terrain

## 3.5 Pedaling Animation (Optional at This Stage)

If a rigged cyclist model is available, import the mesh and configure an `AnimationTree` blending idle and pedal animations by speed. Otherwise, defer to Phase 7 — placeholder geometry is sufficient for controller validation.

---

# Phase 4 — Health Monitor System

The health monitor is the **central simulation mechanic** of this project. It is implemented as a dedicated system, not as an extension of the movement controller.

## 4.1 Design the Health Model

Define a `PlayerHealth` C# class (plain, non-Node) with the following behavior:

- **Passive drain:** Health decreases continuously while the player is pedaling.
- **Effort-scaled drain:** Higher speed and steeper positive gradients increase the drain rate proportionally.
- **Coasting recovery:** Health recovers at a defined rate when the player is not applying pedal input and the gradient is neutral or negative.
- **Consumable recovery:** Food and drink items restore a discrete, item-defined quantity of health on pickup or activation.
- **Bounds:** Health is clamped between `0.0` (exhaustion) and `1.0` (full).
- **Exhaustion state:** At `0.0`, the player enters a reduced-speed or forced-stop state.

## 4.2 Integrate with Movement

The health system directly influences movement parameters:
- Drain rate is a function of current speed and road gradient (degrees).
- Recovery rate applies when speed falls below a configurable coasting threshold.
- Movement scripts query health state to apply fatigue-based speed penalties.

## 4.3 Food and Drink Pickup System

Implement consumable items as scene instances placed along the stage route:
- Each consumable defines a health restoration value and type (food vs. drink).
- Pickup is triggered by proximity collision with `PlayerBike`.
- Items are removed from the scene on collection.

---

# Phase 5 — Terrain and Stage Environment

Once the cyclist controller and health system are validated in a test scene, construct the stage world.

## 5.1 Choose a Terrain Workflow

| Option | Description | Best For |
|---|---|---|
| **Heightmap Plugin** (recommended) | Sculpt-based, good performance, supports large landscapes | Production stages |
| **Blender Sculpt -> Godot** | High artistic control, manual export workflow | Specific iconic climbs |
| **Procedural Terrain** | Fast iteration, not suitable for authored stage routes | Early prototyping only |

## 5.2 Import and Configure Terrain

- Add `CollisionShape3D` to all terrain mesh surfaces.
- Apply Level of Detail (LOD) configuration if supported by the terrain plugin.
- Apply basic surface textures: grass, rock, road surface.

## 5.3 Road Spline

Use a spline-based road tool to define the riding route:
- Smooth road surface geometry
- Banked turns
- Gradient transitions between climb, flat, and descent segments

## 5.4 Validate Cyclist on Real Terrain

Perform integration testing of the controller and health system on actual terrain:
- Speed behavior on climbs and descents
- Drain and recovery rates against designed elevation profiles
- Turning radius on narrow road sections
- Camera behavior on steep gradients

---

# Phase 6 — Stage Data and Progression System

## 6.1 Define the Stage Data Model

Each stage is a data resource defining:
- Stage name and identifier
- Total route distance (km)
- Elevation profile (array of distance/altitude pairs)
- Consumable item placement along the route
- Par time (target completion time)

Implement stages as Godot `Resource` subclasses (C# `[GlobalClass]`) for editor integration.

## 6.2 Stage Manager

Implement a `StageManager` node responsible for:
- Loading and initializing stage data
- Tracking player progress along the route
- Triggering start, checkpoint, and finish events
- Evaluating stage completion conditions

## 6.3 Stage Selection

Implement a stage selection screen that displays available stages with name, distance, and elevation summary, and allows the player to select and load a stage.

---

# Phase 7 — Real Assets and Animations

## 7.1 Import Final Cyclist Model

- Rigged mesh with skeletal animation
- Pedaling cycle animation (slow and fast variants)
- Leaning animation (left/right turns)

## 7.2 AnimationTree Configuration

Configure an `AnimationTree` to blend:
- Idle
- Pedal (speed-mapped)
- Lean left / lean right

## 7.3 Audio

Add the following audio elements:
- Wind noise (speed-mapped volume)
- Tire/road surface contact noise
- Gear shifting sound
- Pedaling cadence rhythm

---

# Phase 8 — UI and Game Loop

- **HUD:** Speed, cadence, road gradient, health indicator
- **Stage start/finish logic:** Countdown timer, elapsed time, result screen
- **Stage selection:** Entry point into stage scenes
- **Pause menu:** Resume, settings, quit
- **Settings persistence:** Save/load via Godot `ConfigFile`

---

# Phase 9 — Additional Simulation Mechanics

With the core loop stable, extend the simulation with:

- Gear shifting (gear ratio affects cadence efficiency and drain rate)
- Drafting / slipstream (reduced aerodynamic drag when following another rider)
- Aerodynamic drag (speed-squared resistance penalty)
- Cornering grip (speed limit on turns proportional to turn radius)

---

# Phase 10 — Polish and Optimization

- LOD configuration and tuning
- Lightmap baking for static terrain
- Shadow quality settings
- Physics solver tuning
- Input smoothing and dead zone calibration
- Camera edge-case polish (tunnels, steep descents, sharp turns)

---

# Design Rationale

The development sequence — controller first, terrain second, simulation mechanics third — reflects a deliberate risk-reduction strategy. The most uncertain and most-iterated element of any vehicle or character game is the feel of movement. Validating this in isolation, on a minimal test scene, eliminates the cost of re-tuning physics against a complex environment. Terrain and content become meaningful only after the controller establishes a stable, satisfying baseline.

The health monitor is treated as a first-class system rather than a late-stage addition because it is the primary differentiator of this project's design. Its behavior must be validated against the movement system early to ensure that drain rates, recovery curves, and consumable values produce a balanced and engaging experience across varying stage elevation profiles.
