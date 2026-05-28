# GODOT4 Cycle Tour

A 3D bicycling simulation built with **Godot 4** and **C#**, developed as an educational project exploring Godot 4, AI-assisted development, and spec-driven development with AI OpenSpec.

> **Note:** This project is created for educational and demonstration purposes only. It will not be monetized.

---

## Project Overview

Cycle Tour is a stage-based cycling simulation inspired by multi-stage races such as the Tour de France. The player manages a **health-based stamina system** while riding through varied 3D landscapes across multiple stages.

### Core Gameplay Mechanics

- **Health Monitor** — Riding continuously consumes health. Harder or faster riding depletes it more quickly.
- **Recovery** — Coasting (free-wheeling) allows health to recover. Consuming food and drink while riding also restores health.
- **Stage Progression** — The game is structured as a series of stages, each with a distinct length and elevation profile that directly affects riding difficulty and health consumption.
- **Realistic Landscape** — Stages feature procedurally or hand-crafted 3D terrain reflecting realistic elevation changes (climbs, descents, flats).

---

## Technology Stack

| Layer | Technology |
|---|---|
| Game Engine | Godot 4 |
| Language | C# (.NET) |
| Project Management | AI OpenSpec (spec-driven development) |
| AI Tooling | Windsurf / Cascade |
| Version Control | Git / GitHub |

---

## Educational Goals

This project is intentionally used as a learning vehicle for:

1. **Godot 4** — Scene system, 3D rendering, physics, GDScript ↔ C# interop, signals, resources, and the Godot editor workflow.
2. **C# in Godot** — Applying .NET C# patterns within the Godot node/scene architecture.
3. **AI-Assisted Development** — Pair programming with AI (Windsurf/Cascade) to accelerate implementation and explore best practices.
4. **Spec-Driven Development (AI OpenSpec)** — Using OpenSpec to define, track, and implement features through structured change proposals and specifications before writing code.

---

## Project Structure

```
GODOT4_CycleTour/
├── openspec/               # AI OpenSpec artifacts
│   ├── config.yaml         # OpenSpec project configuration
│   ├── changes/            # Change proposals and implementation records
│   └── specs/              # Feature specifications
├── .windsurf/              # Windsurf AI tooling (workflows, skills)
├── LICENSE                 # MIT License
└── README.md               # This file
```

> Game source files (Godot scenes, scripts, assets) will be added under the project root as development progresses.

---

## Getting Started

### Prerequisites

- [Godot 4.x](https://godotengine.org/download) with .NET support enabled (the `mono`/`.NET` build)
- [.NET SDK 8.0+](https://dotnet.microsoft.com/download)
- A C#-capable IDE (e.g., Visual Studio, VS Code with C# Dev Kit, or Rider)

### Setup

1. Clone the repository:
   ```bash
   git clone https://github.com/Bills-Institute-of-Technology/GODOT4_CycleTour.git
   ```
2. Open the project folder in Godot 4 (`Import` → select the project folder).
3. Allow Godot to import assets and generate the `.godot/` cache on first open.
4. Build the C# solution from the Godot editor (`MSBuild` button or `Build > Build Solution`).

---

## Development Workflow

This project uses **AI OpenSpec** for spec-driven development. The intended workflow is:

1. **Explore** — Use `openspec-explore` to think through a feature or problem.
2. **Propose** — Use `openspec-propose` to generate a structured change proposal with design, specs, and tasks.
3. **Implement** — Use `openspec-apply-change` to work through implementation tasks one at a time.
4. **Archive** — Use `openspec-archive-change` to finalize and record completed changes.

All proposals and specs are stored under `openspec/`.

---

## License

MIT License — see [LICENSE](LICENSE) for details.

Copyright © 2026 Bill's Institute of Technology
