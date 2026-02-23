# QET-Gen IDE — Vision & Goals

> Browser-based IDE for industrial electrical schematics, powered by AI conversation.

## The Problem

Creating industrial electrical schematics today requires:
- Expensive desktop software (EPLAN, SEE Electrical) or limited free tools (QElectroTech)
- Manual placement of every component, wire, and label
- Deep knowledge of IEC/DIN standards for correct symbol usage
- Hours of repetitive work for standard circuits (DOL starters, VFD drives, PLC I/O)

**AI can generate schematic descriptions (ESCL) very well**, but turning that into a visual, editable schematic is the hard part.

## The Vision

A **browser-based IDE** where engineers describe what they need in natural language, and the system generates professional electrical schematics in real-time.

```
┌─────────────────────────────────────────────────────────┐
│  Chat Panel          │  Schematic Canvas (SVG)          │
│                      │                                  │
│  "Add a 5.5kW motor  │   L1  L2  L3                    │
│   with VFD, fed from │   │   │   │                     │
│   a 3-pole breaker"  │  ┌┴───┴───┴┐                    │
│                      │  │ -Q1  16A │                    │
│  ✅ Added:           │  └┬───┬───┬┘                    │
│  • Breaker -Q1 16A   │   │   │   │                     │
│  • VFD -U1 5.5kW     │  ┌┴───┴───┴┐                    │
│  • Motor -M1 5.5kW   │  │ -U1 VFD │                    │
│  • Cables -W1, -W2   │  └┬───┬───┬┘                    │
│                      │   │   │   │                     │
│  "Add thermal         │  ┌┴───┴───┴┐                    │
│   overload relay"     │  │  M  3~  │                    │
│                      │  └─────────┘                    │
│                      │                                  │
│  Component Library ▼ │  [Zoom] [Pan] [Export]          │
└─────────────────────────────────────────────────────────┘
```

### Core Interaction Modes

1. **Chat-driven** — Describe the circuit in natural language, AI generates it
2. **Drag & drop** — Pick from component library, place on canvas
3. **Hybrid** — Place components manually, ask AI to wire them
4. **Import** — Load existing ESCL files or QET projects

## Key Goals

### 1. Reuse QElectroTech's Component Library (6760+ elements)

QET elements are XML files with simple graphical primitives:
```xml
<line x1="0" y1="-20" x2="0" y2="30" style="..." />
<rect x="-10" y="-10" width="20" height="30" style="..." />
<circle x="0" y="0" diameter="30" />
<arc x="-8" y="-10" width="16" height="10" start="0" angle="180" />
<terminal x="0" y="-20" name="1" orientation="n" />
```

These map **almost 1:1 to SVG**:
- `<line>` → `<line>`
- `<rect>` → `<rect>`
- `<circle>` → `<circle>`
- `<arc>` → `<path>` with arc commands
- `<ellipse>` → `<ellipse>`
- `<polygon>` → `<polygon>`
- `<text>` → `<text>`
- `<terminal>` → connection points (rendered as small circles/squares)

**Day 1**: Import entire QET library → 6760 components ready to use.

### 2. ESCL as the AI ↔ Engine Bridge

ESCL (Electrical Schematic Context Language) is our intermediate format that LLMs generate well:

```
breaker -Q1 "Main Breaker 16A" {
  rating: 16A
  <- L1
}

contactor -KM1 "Main Contactor" {
  <- -Q1
}

motor -M1 "Pump 5.5kW" {
  power: 5.5kW
  voltage: 400V
  <- -KM1
}
```

The AI generates/modifies ESCL → Parser → IR → Layout Engine → SVG Renderer.

### 3. Professional Output

- IEC 60617 / DIN compliant symbols
- Proper page layout (title block, column/row grid, cross-references)
- Wire numbering and cable tags
- Multi-page projects with cross-page references
- Export: PDF, SVG, DXF, and QET format

### 4. Smart Features

- **Auto-wiring**: Place components, AI connects them correctly
- **3-phase detection**: Automatically uses 3-pole elements in power circuits
- **Standard circuits**: "Add a DOL starter" generates the full standard circuit
- **Validation**: Warns about missing protection, incorrect wiring, code violations
- **Cross-references**: Contactor coil ↔ contacts linked automatically

## Architecture

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Chat UI    │────▶│  AI Engine   │────▶│ ESCL Parser  │
│  (React)     │◀────│ (LLM API)   │     │              │
└──────────────┘     └──────────────┘     └──────┬───────┘
                                                 │
┌──────────────┐     ┌──────────────┐     ┌──────▼───────┐
│  SVG Canvas  │◀────│  SVG Render  │◀────│   IR Graph   │
│  (interact)  │     │  Engine      │     │ + Layout     │
└──────┬───────┘     └──────────────┘     └──────────────┘
       │
       ▼
┌──────────────┐     ┌──────────────┐
│  Export      │     │  QET Element │
│  PDF/SVG/DXF │     │  Importer    │
└──────────────┘     └──────────────┘
```

### Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | React + TypeScript | Our standard stack |
| Canvas | SVG (not Canvas API) | Scalable, inspectable, CSS-stylable, accessible |
| State | Zustand or similar | Lightweight, good for real-time updates |
| AI | OpenAI/Anthropic API | ESCL generation |
| ESCL Parser | Existing (from qet-gen) | Already built and working |
| IR + Layout | Existing (from qet-gen) | Core logic reusable |
| QET Import | New — XML parser | Parse .elmt files → SVG components |
| Export | jsPDF, svg-to-dxf | Standard conversions |

### What We Already Have (from qet-gen)

- ✅ ESCL parser (`src/parser/escl-parser.ts`)
- ✅ Internal Representation types (`src/ir/types.ts`)
- ✅ Layout engine (`src/layout/engine.ts`)
- ✅ Grid system (`src/layout/grid.ts`)
- ✅ 3-phase detection logic (`src/transform.ts`)
- ✅ 15 component definitions with graphical primitives
- ✅ ESCL specification document

### What We Need to Build

1. **QET Element Importer** — Parse .elmt XML → SVG component definitions
2. **SVG Renderer** — IR graph → interactive SVG on canvas
3. **Wire Router** — Auto-route conductors avoiding overlaps (orthogonal routing)
4. **Chat UI** — Conversation panel with AI integration
5. **Canvas Interaction** — Zoom, pan, select, move, delete
6. **Component Palette** — Searchable library browser
7. **Page System** — Multi-page projects with title blocks
8. **Export Engine** — PDF, SVG, DXF output

## Phases

### Phase 1 — Proof of Concept (1-2 weeks)
- QET element importer (parse .elmt → SVG)
- Basic SVG canvas with zoom/pan
- Render a simple circuit (motor starter) from ESCL
- Validate that QET elements render correctly as SVG

### Phase 2 — Interactive Canvas (2-3 weeks)
- Component palette with search
- Drag & drop placement
- Click-to-connect wiring
- Select, move, delete components
- Undo/redo

### Phase 3 — AI Chat Integration (1-2 weeks)
- Chat panel UI
- AI generates/modifies ESCL from natural language
- Real-time canvas updates as AI responds
- Context awareness (AI sees current schematic state)

### Phase 4 — Professional Output (2-3 weeks)
- Title block with project info
- Wire numbering
- Cross-references between pages
- PDF export with proper pagination
- SVG/DXF export

### Phase 5 — Advanced Features
- Component database with specs (ratings, dimensions)
- Validation engine (missing protection, overload checks)
- Standard circuit templates
- Collaboration (multi-user editing)
- QET project import/export (bidirectional)

## Name

Working title: **QET-Gen IDE** (or new name TBD)

Options to discuss:
- **SchematicAI** — generic, clear
- **WireGen** — short, memorable
- **CircuitForge** — implies building/crafting
- Keep **QET-Gen** and evolve it

## Competitive Landscape

| Tool | Price | AI? | Browser? | Industrial? |
|------|-------|-----|----------|-------------|
| EPLAN | €5k+/yr | No | No | ✅ |
| SEE Electrical | €2k+/yr | No | No | ✅ |
| QElectroTech | Free | No | No | ✅ |
| AutoCAD Electrical | €2k+/yr | No | No | ✅ |
| **QET-Gen IDE** | **TBD** | **✅** | **✅** | **✅** |

No one is doing AI + browser + industrial schematics today. This is a blue ocean.

## Open Questions

1. **Monetization**: SaaS subscription? Per-export? Freemium?
2. **Name & branding**: Standalone product or part of AnlageAI?
3. **Target audience**: Electricians? Engineers? Both?
4. **Offline support**: PWA with local storage?
5. **QET compatibility**: Import only, or full bidirectional?
6. **Self-hosted option**: For companies that can't use cloud?

---

*Created: 2026-02-23*
*Authors: Romulo & Claw*
