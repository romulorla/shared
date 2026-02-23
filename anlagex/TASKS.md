# AnlageX — Tasks & Roadmap

## ✅ Done

### Phase 1 — Canvas & Component Library
- [x] React + TypeScript + Vite + Tailwind CSS 4 + Zustand setup
- [x] 3-panel layout (component tree, canvas, chat/properties)
- [x] SVG canvas with grid, zoom (ctrl+scroll), pan (shift+drag)
- [x] ESCL parser + IR types copied from qet-gen
- [x] QET element XML → SVG renderer
- [x] 15+ embedded electrical components
- [x] Wire renderer with orthogonal routing
- [x] Demo scene (DOL motor starter)
- [x] Component palette with 6 categories, SVG previews, search
- [x] Drag & drop from palette to canvas (snap to grid, auto-tag)
- [x] Component selection & movement (click, drag, Delete)
- [x] Click-to-wire between terminals
- [x] Undo/redo (Ctrl+Z / Ctrl+Shift+Z)
- [x] Invisible hit area on components for easier selection

### Phase 2 — Professional Features
- [x] Wire numbering (auto W1/W2/W3, click to edit)
- [x] Component tag management + inline editing
- [x] Properties panel (Label, Type, Description, Manufacturer, etc.)
- [x] Multi-page support with page tabs
- [x] Title block with project info
- [x] Text annotations tool
- [x] PDF export (A4 landscape)
- [x] SVG export
- [x] Save/load projects (.anlagex.json)

### Phase 3 — AI Chat Integration
- [x] Chat panel UI (messages, streaming, typing indicator)
- [x] OpenAI + Anthropic support (API key in localStorage)
- [x] AI generates ESCL → auto-applied to canvas with diff summary
- [x] Bidirectional context (AI sees current schematic as ESCL)
- [x] Quick actions (DOL Starter, Thermal Overload, E-Stop, Explain)

### Phase 4 — Import & Interoperability
- [x] QET project importer (.qet → canvas)
- [x] QET element library importer (.elmt bulk + ZIP, IndexedDB)
- [x] ESCL import/export
- [x] DXF export (AutoCAD compatible)
- [x] File menu (New, Open, Import, Export, Save)

### UI Overhaul
- [x] Menu bar (File, Edit, Display, Project, Help)
- [x] Icon toolbar with monochrome SVG icons
- [x] Project panel in sidebar (page list)
- [x] Schematic sheet with column/row indicators
- [x] Light theme default (dark mode disabled for now)

## 🔄 In Progress

- [ ] **EPLAN-style sheet template** — columns 0-9, professional title block, no row letters, navy blue text

## 📋 Backlog — Short Term

### Visual Polish (match EPLAN reference)
- [ ] **Power rails** — horizontal L1/L2/L3 lines at top with cross-references (origin page.column)
- [ ] **Cross-reference system** — show where related contacts/coils are (e.g., /53.1 next to contactor)
- [ ] **Component specs inline** — part number, rating, range shown below component tag
- [ ] **Cable blocks** — dashed-line boxes with cable type, spec, gland, length, wire colors
- [ ] **Terminal strips** — XK terminals with numbered connection points
- [ ] **Wire colors** at cable endpoints (1, 2, 3, GNYE, BK, WH)
- [ ] **Function descriptions** below motors/drives (bilingual DE/EN)
- [ ] **Page cross-refs** — arrows with page.column at rail endpoints
- [ ] **Connection dots** at wire junctions

### Editor Features
- [ ] **Copy/paste** components (Ctrl+C/V)
- [ ] **Multi-select** (Shift+click or rubber band)
- [ ] **Snap guidelines** when moving components
- [ ] **Right-click context menu** (delete, copy, properties, rotate)
- [ ] **Component rotation** (90°/180°/270°)
- [ ] **Keyboard shortcuts** complete set
- [ ] **Zoom to fit** all content
- [ ] **Minimap** for large schematics

### Dark Mode Fix
- [ ] Fix component visibility in dark theme (stroke colors)
- [ ] Re-enable theme toggle

### ESCL Improvements
- [ ] **ESCL editor pane** — view/edit raw ESCL alongside canvas
- [ ] **Live sync** — edit ESCL text → canvas updates, edit canvas → ESCL updates
- [ ] **ESCL validation** — highlight errors in ESCL text

## 📋 Backlog — Medium Term

### Professional Output
- [ ] **Title block editor** — UI to fill project info (customer, drawing no., etc.)
- [ ] **A3 page support** (in addition to A4)
- [ ] **Print-ready PDF** with proper margins and scaling
- [ ] **Bill of Materials (BOM)** auto-generated from components
- [ ] **Wire list** export

### Advanced Schematics
- [ ] **Contactor coil ↔ contact linking** (automatic cross-references)
- [ ] **PLC I/O pages** — structured PLC module representation
- [ ] **VFD/Drive blocks** — detailed terminal representation (like SEW Eurodrive in reference)
- [ ] **Soft starter representation**
- [ ] **Safety circuits** — PROFIsafe modules, safety relays
- [ ] **Single-line diagrams** (power distribution overview)

### Import/Export
- [ ] **EPLAN XML import** (if format is documented)
- [ ] **PDF → ESCL** via AI (using ESCL Generator skill)
- [ ] **Photo → ESCL** via AI vision
- [ ] **QET project export** (bidirectional)

## 📋 Backlog — Long Term

- [ ] **User accounts** (Supabase auth)
- [ ] **Cloud project storage**
- [ ] **Collaboration** (multi-user editing)
- [ ] **Custom component editor** (create new symbols)
- [ ] **Community component library** (share/download)
- [ ] **Component database** with manufacturer catalogs (Siemens, ABB, Schneider)
- [ ] **Validation engine** (missing protection, overload checks, code compliance)
- [ ] **Standard circuit templates** (DOL, star-delta, VFD, reversing, etc.)
- [ ] **Auto-numbering** (wires, components, pages)
- [ ] **Offline PWA** mode

---

*Last updated: 2026-02-23*
