# Procedural Building Generator (`procbuild`) — Architecture & Render Showcase

A rule-driven, headless-capable procedural 3D building generator for **Blender**, designed to produce game-ready architectural structures, interiors, trim-sheet UV layouts, colliders, and **Enfusion / Arma Reforger** export pipelines.

> **Note:** This repository is a **documentation and render showcase** only. The `procbuild` source code, asset libraries, and mod-making rules are kept private.

---

## Headless Blender Renders

Generated directly from parametric presets in headless Blender 5.2:

| Apartments (`abandoned_apartments`) | Hotel (`americana_main_hotel`) | Theater (`americana_main_theater`) |
|---|---|---|
| ![Apartments](docs/images/apartments.jpg) | ![Hotel](docs/images/hotel.jpg) | ![Theater](docs/images/theater.jpg) |

| Chongqing Skybridge Towers (`chongqing_skybridge_towers`) | Fujian Tulou (`chinese_tulou_round_3`) | Normandy Timber Manor (`normandy_manor_timber_manor`) |
|---|---|---|
| ![Chongqing Skybridge](docs/images/chongqing_skybridge.jpg) | ![Tulou](docs/images/tulou.jpg) | ![Normandy Manor](docs/images/normandy_manor.jpg) |

| Flak Tower (`wehrmacht_flak_tower`) | Diagrid Tower (`london_gherkin_tower`) | Roadside Diner (`americana_road_diner`) |
|---|---|---|
| ![Flak Tower](docs/images/flak_tower.jpg) | ![Gherkin Tower](docs/images/gherkin_tower.jpg) | ![Road Diner](docs/images/road_diner.jpg) |

### Parametric Destruction & Rubble (`washington_capitol`)

| Intact | Damaged (`ruin="BITE"`, shell holes & rubble apron) |
|---|---|
| ![US Capitol](docs/images/capitol.jpg) | ![US Capitol Rubble](docs/images/capitol_rubble.jpg) |

---

## Architecture Overview

`procbuild` cleanly separates **pure-Python procedural geometry & layout** (`procbuild/core/`) from the **Blender `bpy` adapter** (`procbuild/blender/`), keeping the core architectural grammar deterministic, testable, and independent of Blender's UI.

```mermaid
flowchart TB
    subgraph Config["Configuration & Asset Registries"]
        P["Style & Shopfront JSON Presets"]
        A["Aperture, Door, Window & Rule Schemas"]
        T["Trim-Sheet & Texture Library Resolver"]
    end

    subgraph Core["procbuild.core (Pure Python - No bpy)"]
        SP["Parametric Spec"] --> FP["Footprint & Inner Rects"]
        FP --> LY["Room Partitioning & Circulation"]
        LY --> WL["Exterior/Interior Walls & OpeningPlanner"]
        WL --> DT["Facades, Roofs, Stairs, Structures & Weathering"]
        DT --> RES["Result (LODs, Colliders, Sockets, Props, Pieces)"]
    end

    subgraph Blender["procbuild.blender (bpy Adapter)"]
        OP["Addon Operators & UI"] --> OB["Scene Builder & Exporter"]
        OB --> MT["Procedural Materials & Trim-Sheet UVs"]
        OB --> GR["Greebles, Decals, Backskin & Edge Wear"]
        OB --> EX["Destruction States, UBX Colliders & Enfusion/FBX"]
    end

    P --> SP
    A --> WL
    T --> MT
    RES --> OB
```

### Modular Style Generators (`procbuild.core`)

Beyond standard corridor and grid buildings, the core engine supports modular architectural grammar pipelines that all emit the same unified `Result` contract:

```mermaid
flowchart LR
    B["Core Build Pipeline"] --> R["Americana & Civic"]
    B --> C["Chongqing & Tulou"]
    B --> CO["Colombage & Courtyard"]
    B --> M["Military, Bunker, FOB & Trench"]
    B --> I["Towers, Domes, Brutalist & Ruin"]
```

---

## Key Engineering Highlights

- **Pure-Python Core vs. `bpy` Adapter:** Geometry generation, room partitioning, opening placement, and collider extraction run in pure Python, allowing 120+ automated structural/layout unit tests to execute outside Blender.
- **Parametric Destruction (`ruin`):** Supports `BITE`, `SHELL`, `CROWN`, `FRAME`, `LEAN`, and `PANCAKE` damage modes—cutting walls, floor slabs, facades, and colliders together while generating rebar, hanging slabs, shell holes, and rubble aprons.
- **Game-Engine Ready:** Emits multi-LOD meshes, greedy-merged `UBX` / convex-hull colliders, attachment sockets, and trim-sheet UV layouts tailored for **Enfusion / Arma Reforger**.
- **Headless Automation:** Full CLI/headless Blender support for batch generation, structural validation, and automated preview rendering.
