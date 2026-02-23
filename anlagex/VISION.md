# AnlageX — Vision & Goals

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

### 1. ESCL is the Source of Truth

**ESCL (Electrical Schematic Context Language)** is the core document format of AnlageX. Everything revolves around it.

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

The canvas is a **bidirectional ESCL editor**:
- **ESCL → Canvas**: Parses ESCL, builds the IR graph, renders as interactive SVG
- **Canvas → ESCL**: Every user action (drag, connect, delete) updates the underlying ESCL

This means:
- **Projects are ESCL files** — human-readable, versionable (git-friendly), lightweight
- **AI reads and writes ESCL** — LLMs are great at structured text
- **Universal import**: Any schematic from any software can be converted to ESCL → rendered in AnlageX
  - EPLAN project → ESCL → AnlageX
  - QElectroTech project → ESCL → AnlageX
  - AutoCAD Electrical → ESCL → AnlageX
  - **PDF scan → AI generates ESCL → AnlageX** (we already have this: ESCL Generator skill)
  - **Photo of paper schematic → AI vision → ESCL → AnlageX**
- **ESCL becomes the Markdown of electrical schematics** — a universal, readable interchange format

### 2. Workspace First, AI Second

The priority is a **professional schematic editor** that stands on its own:
- Familiar layout: component tree (left), canvas (center), properties/chat (right)
- Same workflow as EPLAN/QET — engineers feel at home immediately
- Drag & drop from library, click-to-wire, select/move/delete
- Full keyboard shortcuts
- The AI chat panel is collapsible — use it or ignore it

### 3. Reuse QElectroTech's Component Library (6760+ elements)

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

### 4. Industrial Electrical Schematics Only

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
- Wires follow the grid, user places them or AI suggests paths

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        Frontend (React)                           │
│                                                                  │
│  ┌──────────┐  ┌───────────────────┐  ┌────────────────────────┐ │
│  │Component  │  │   SVG Canvas      │  │   AI Chat Panel       │ │
│  │Tree       │  │   (workspace)     │  │   (collapsible)       │ │
│  │(library)  │  │                   │  │                       │ │
│  └─────┬─────┘  └─────────┬─────────┘  └───────────┬───────────┘ │
│        │                  │                         │             │
│        │          ┌───────▼────────┐                │             │
│        └─────────▶│  ESCL Document │◀───────────────┘             │
│  drag/drop        │  (source of    │  AI reads/writes ESCL       │
│  adds to ESCL     │   truth)       │                             │
│                   └───────┬────────┘                             │
│                           │                                      │
│                   ┌───────▼────────┐                             │
│                   │  ESCL Parser   │                             │
│                   │  → IR Graph    │                             │
│                   │  → SVG Render  │                             │
│                   └────────────────┘                             │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
                            │
              ┌─────────────┼──────────────┐
              ▼             ▼              ▼
┌──────────────────┐ ┌───────────┐ ┌──────────────┐
│  Backend API     │ │ Component │ │  Import      │
│  - Project CRUD  │ │ Store     │ │  Converters  │
│  - AI relay      │ │ (SVG lib) │ │  EPLAN→ESCL  │
│  - Export engine │ │           │ │  QET→ESCL    │
│  - Auth          │ │           │ │  PDF→ESCL    │
└──────────────────┘ └───────────┘ └──────────────┘
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

### What We Already Have

- ✅ ESCL specification document
- ✅ ESCL parser (`qet-gen/src/parser/escl-parser.ts`)
- ✅ ESCL Generator skill (PDF → ESCL, already working in OpenClaw)
- ✅ Internal Representation types (`qet-gen/src/ir/types.ts`)
- ✅ Layout engine (`qet-gen/src/layout/engine.ts`)
- ✅ Grid system (`qet-gen/src/layout/grid.ts`)
- ✅ Transform logic (`qet-gen/src/transform.ts`)
- ✅ 15 component definitions with graphical primitives

## Phases

### Phase 1 — Canvas & Component Library (3-4 weeks)
**Goal: A working schematic editor without AI.**

- QET element importer → parse .elmt XML → our SVG component format
- Import full QET library (6760+ elements), categorize into tree structure
- SVG canvas workspace with grid, zoom, pan
- Component tree on the left (searchable, categorized)
- Drag & drop from library to canvas → generates ESCL
- Click-to-wire: click terminal → click terminal → wire added to ESCL
- Select, move, delete, copy/paste components
- Properties panel for selected component
- Undo/redo (ESCL history stack)
- Page system (add/remove/reorder pages)
- Title block with project info
- Save/load projects as ESCL files

### Phase 2 — Professional Features (2-3 weeks)
**Goal: Feature parity with basic QElectroTech usage.**

- Wire numbering (auto or manual)
- Component tag management (-Q1, -KM1, etc.)
- Cross-references between pages (coil ↔ contacts)
- Connection dots at wire junctions
- Text annotations and labels
- PDF export with proper pagination and title blocks
- SVG export
- Project management (backend)

### Phase 3 — AI Chat Integration (2-3 weeks)
**Goal: AI assistant that understands the current schematic.**

- Collapsible chat panel on the right
- AI sees current schematic state as ESCL
- Natural language → ESCL modifications → canvas updates in real-time
- "Add a DOL starter for a 5.5kW motor"
- "Add thermal overload between contactor and motor"
- "Create the control circuit for KM1"
- AI suggestions (hover to preview, click to accept)

### Phase 4 — Import & Interoperability (2-3 weeks)
**Goal: Import schematics from anywhere.**

- QET project → ESCL converter
- PDF schematic → ESCL (via AI / ESCL Generator skill)
- Photo → ESCL (via AI vision)
- EPLAN XML → ESCL converter
- DXF export (for AutoCAD compatibility)

### Phase 5 — Advanced (ongoing)
- Component database with manufacturer specs
- Validation engine (missing protection, incorrect wiring)
- Standard circuit templates (DOL, star-delta, VFD, etc.)
- Custom component editor
- Collaboration (multi-user)
- Offline PWA mode

## Competitive Landscape

| Tool | Price | AI? | Browser? | Industrial? | Import? |
|------|-------|-----|----------|-------------|---------|
| EPLAN | €5k+/yr | No | No | ✅ | Own format |
| SEE Electrical | €2k+/yr | No | No | ✅ | Own format |
| QElectroTech | Free | No | No | ✅ | Own format |
| AutoCAD Electrical | €2k+/yr | No | No | ✅ | DWG/DXF |
| **AnlageX** | **TBD** | **✅** | **✅** | **✅** | **Universal (ESCL)** |

No one is doing AI + browser + industrial electrical schematics today. And no one has a universal interchange format for electrical schematics.

## Decisions

1. **Name**: ✅ **AnlageX** — part of the Anlage ecosystem (AnlageAI, AnlageX)
2. ~~Monetization~~: Not a priority for MVP
3. **Target audience**: All — engineers, electricians, panel builders, students. Markets: Germany & Brazil first.
4. **Offline**: Online only for now
5. **QET compatibility**: Import only
6. ~~Self-hosted~~: Not for MVP
7. **Community components**: Yes, but not MVP priority

---

*Created: 2026-02-23*
*Authors: Romulo & Claw*
