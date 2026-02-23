# QET-Gen IDE — Vision & Goals

> Browser-based IDE for industrial electrical schematics — hybrid drag & drop + AI assistant.

## The Problem

Creating industrial electrical schematics today requires:
- Expensive desktop software (EPLAN €5k+/yr, SEE Electrical €2k+/yr) or limited free tools (QElectroTech)
- Manual placement of every component, wire, and label
- Deep knowledge of IEC/DIN standards for correct symbol usage
- Hours of repetitive work for standard circuits (DOL starters, VFD drives, PLC I/O)

## The Vision

A **browser-based IDE** that works like EPLAN or QElectroTech — full drag & drop workspace with component libraries — **plus** an optional AI assistant that can generate and modify schematics through conversation.

**The AI is a feature, not the product.** The platform must be fully functional without AI. Engineers who prefer traditional workflows use drag & drop. Engineers who want speed use the AI chat. Most will use both.

```
┌───────────────────────────────────────────────────────────────────┐
│  Menu / Toolbar                                                   │
├──────────────┬────────────────────────────────┬───────────────────┤
│              │                                │                   │
│  Component   │     Schematic Canvas (SVG)     │   AI Chat         │
│  Library     │                                │   (optional)      │
│              │   L1  L2  L3                   │                   │
│  ▼ Protection│   │   │   │                    │  "Add thermal     │
│    Breakers  │  ┌┴───┴───┴┐                   │   overload after  │
│    Fuses     │  │ -Q1  16A │                  │   the contactor"  │
│    RCDs      │  └┬───┬───┬┘                   │                   │
│  ▼ Contactors│   │   │   │                    │  ✅ Added -F1     │
│    Coils     │  ┌┴───┴───┴┐                   │  between -KM1     │
│    Contacts  │  │-KM1     │                   │  and -M1          │
│  ▼ Motors    │  └┬───┬───┬┘                   │                   │
│    3-phase   │   │   │   │                    │                   │
│    1-phase   │  ┌┴───┴───┴┐                   │                   │
│  ▼ PLCs      │  │  M  3~  │                   │                   │
│    Siemens   │  └─────────┘                   │                   │
│    AB        │                                │                   │
│  ▼ Sensors   │  Page 1 of 12    Zoom: 100%    │                   │
│              │                                │                   │
├──────────────┴────────────────────────────────┴───────────────────┤
│  Status bar: Project: Motor Starter | Modified | IEC 60617        │
└───────────────────────────────────────────────────────────────────┘
```

## Key Principles

### 1. Workspace First, AI Second

The priority is a **professional schematic editor** that stands on its own:
- Familiar layout: component tree (left), canvas (center), properties/chat (right)
- Same workflow as EPLAN/QET — engineers feel at home immediately
- Drag & drop from library, click-to-wire, select/move/delete
- Full keyboard shortcuts
- The AI chat panel is collapsible — use it or ignore it

### 2. Reuse QElectroTech's Component Library (6760+ elements)

QET elements are XML files with simple graphical primitives. We parse them and convert to our **own internal SVG-based component format**, stored on our backend.

QET XML primitives → Our SVG component format:
| QET XML | Our SVG |
|---------|---------|
| `<line x1 y1 x2 y2>` | `<line>` |
| `<rect x y width height>` | `<rect>` |
| `<circle x y diameter>` | `<circle>` |
| `<arc x y width height start angle>` | `<path d="...">` |
| `<ellipse x y width height>` | `<ellipse>` |
| `<polygon>` | `<polygon>` |
| `<text>` | `<text>` |
| `<terminal x y orientation>` | Connection point metadata |
| `<dynamic_text>` | Editable text fields |

**Our component format** (stored in backend):
```json
{
  "id": "breaker-3p",
  "name": { "en": "3-Pole Circuit Breaker", "de": "Leitungsschutzschalter 3-polig" },
  "category": ["protection", "circuit_breakers"],
  "standard": "IEC 60617",
  "svg": "<svg viewBox='...'> ... </svg>",
  "terminals": [
    { "name": "1/L1", "x": -20, "y": -20, "orientation": "n" },
    { "name": "2/T1", "x": -20, "y": 30, "orientation": "s" }
  ],
  "properties": {
    "rating": { "type": "string", "label": "Rating", "default": "16A" },
    "poles": { "type": "number", "label": "Poles", "default": 3 }
  },
  "tags": ["breaker", "mcb", "protection", "3-pole"]
}
```

This gives us:
- **Independence from QET format** — our own format optimized for browser rendering
- **Searchable metadata** — categories, tags, properties
- **Multi-language names** — i18n from day one
- **Extensibility** — users can create custom components

### 3. Industrial Electrical Schematics Only

This is **not** a PCB design tool. We focus exclusively on:
- Power distribution (single-line and multi-line diagrams)
- Motor control circuits
- PLC I/O wiring
- Control circuits (relays, contactors, timers)
- Safety circuits (E-stop, safety relays, light curtains)
- Instrumentation (sensors, transmitters)
- Pneumatic/hydraulic valve control

Wiring on the canvas follows **industrial schematic conventions**:
- Orthogonal lines (horizontal and vertical only)
- Connection dots at wire junctions
- Wire numbering per IEC standards
- Cross-reference system between pages (coil ↔ contacts)
- No auto-routing algorithms — wires follow the grid, user places them or AI suggests paths

### 4. ESCL as the AI ↔ Engine Bridge

ESCL (Electrical Schematic Context Language) remains our intermediate format for AI interaction:

```
breaker -Q1 "Main Breaker 16A" {
  rating: 16A
  poles: 3
  <- L1
}

contactor -KM1 "Main Contactor" {
  rating: 18A
  <- -Q1
}

motor -M1 "Pump 5.5kW" {
  power: 5.5kW
  voltage: 400V
  <- -KM1
}
```

The AI generates/modifies ESCL → Parser → IR → Layout → Canvas update.
But the user can also work entirely without ESCL — just drag, drop, and wire.

## Architecture

```
┌──────────────────────────────────────────────────────────┐
│                      Frontend (React)                     │
│                                                          │
│  ┌─────────┐  ┌──────────────────┐  ┌─────────────────┐ │
│  │Component │  │   SVG Canvas     │  │   AI Chat       │ │
│  │Tree      │  │   (workspace)    │  │   Panel         │ │
│  │(library) │  │                  │  │                 │ │
│  └────┬─────┘  └────────┬─────────┘  └───────┬─────────┘ │
│       │                 │                     │           │
│       └────────┬────────┘                     │           │
│                ▼                              │           │
│  ┌──────────────────────┐                     │           │
│  │   Document Model     │◀────────────────────┘           │
│  │   (IR Graph + State) │     AI modifies via ESCL        │
│  └──────────┬───────────┘                                 │
│             │                                             │
└─────────────┼─────────────────────────────────────────────┘
              │
              ▼
┌──────────────────────┐    ┌──────────────────────┐
│   Backend API        │    │   Component Store     │
│   - Project CRUD     │    │   - SVG definitions   │
│   - AI relay         │    │   - Imported from QET │
│   - Export engine    │    │   - User custom       │
│   - Auth             │    │   - Categorized       │
└──────────────────────┘    └──────────────────────┘
```

### Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | React + TypeScript | Our standard stack |
| Canvas | SVG | Scalable, inspectable, CSS-stylable |
| State | Zustand | Lightweight, good for real-time updates |
| Backend | Node.js + TypeScript | Consistent stack |
| Database | PostgreSQL (Supabase) | Projects, components, users |
| Component Store | Backend + CDN | SVG components served fast |
| AI | OpenAI / Anthropic API | ESCL generation from chat |
| ESCL Parser | Existing (from qet-gen) | Already built |
| Export | jsPDF, SVG native | PDF and SVG output |

### What We Already Have (from qet-gen)

- ✅ ESCL parser (`src/parser/escl-parser.ts`)
- ✅ Internal Representation types (`src/ir/types.ts`)
- ✅ Layout engine (`src/layout/engine.ts`)
- ✅ Grid system (`src/layout/grid.ts`)
- ✅ Transform logic (`src/transform.ts`)
- ✅ 15 component definitions with graphical primitives
- ✅ ESCL specification document

## Phases

### Phase 1 — Canvas & Component Library (3-4 weeks)
**Goal: A working schematic editor without AI.**

- QET element importer → parse .elmt XML → our SVG component format
- Import full QET library (6760+ elements), categorize into tree structure
- SVG canvas workspace with grid, zoom, pan
- Component tree on the left (searchable, categorized)
- Drag & drop from library to canvas
- Click-to-wire: click terminal → click terminal → wire drawn
- Select, move, delete, copy/paste components
- Properties panel for selected component
- Undo/redo
- Page system (add/remove/reorder pages)
- Title block with project info

### Phase 2 — Professional Features (2-3 weeks)
**Goal: Feature parity with basic QElectroTech usage.**

- Wire numbering (auto or manual)
- Component tag management (-Q1, -KM1, etc.)
- Cross-references between pages (coil ↔ contacts)
- Connection dots at wire junctions
- Text annotations and labels
- PDF export with proper pagination and title blocks
- SVG export
- Save/load projects (backend)

### Phase 3 — AI Chat Integration (2-3 weeks)
**Goal: AI assistant that understands the current schematic.**

- Collapsible chat panel on the right
- AI sees current schematic state (serialized as ESCL)
- Natural language → ESCL modifications → canvas updates
- "Add a DOL starter for a 5.5kW motor"
- "Add thermal overload between contactor and motor"
- "Create the control circuit for KM1"
- AI suggestions (hover to preview, click to accept)

### Phase 4 — Advanced (ongoing)
- DXF export (for AutoCAD compatibility)
- Component database with manufacturer specs
- Validation engine (missing protection, incorrect wiring)
- Standard circuit templates (DOL, star-delta, VFD, etc.)
- Custom component editor
- Collaboration (multi-user)
- QET project import/export (bidirectional)
- Offline PWA mode

## Competitive Landscape

| Tool | Price | AI? | Browser? | Industrial? |
|------|-------|-----|----------|-------------|
| EPLAN | €5k+/yr | No | No | ✅ |
| SEE Electrical | €2k+/yr | No | No | ✅ |
| QElectroTech | Free | No | No | ✅ |
| AutoCAD Electrical | €2k+/yr | No | No | ✅ |
| **This project** | **TBD** | **✅** | **✅** | **✅** |

No one is doing AI + browser + industrial electrical schematics today.

## Open Questions

1. **Name & branding**: Standalone product or part of AnlageAI?
2. **Monetization**: SaaS subscription? Freemium (free editor, paid AI + cloud)?
3. **Target audience**: Electricians? Electrical engineers? Panel builders? All?
4. **Offline support**: PWA with IndexedDB for local projects?
5. **QET compatibility**: Import only, or full bidirectional sync?
6. **Self-hosted option**: For companies that can't use cloud?
7. **Component contributions**: Allow community to submit/verify components?

---

*Created: 2026-02-23*
*Authors: Romulo & Claw*
