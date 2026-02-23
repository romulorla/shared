# AnlageX — EPLAN Reference Analysis

Based on 4 professional EPLAN schematics from Prinzing Pfeiffer (TORNADO 150/150 machine).

## Page Layout

### Grid System
- **Columns**: 0–9 numbered at top, evenly spaced
- **No row letters** (unlike QET which has A-H) — EPLAN uses the column numbers and vertical position
- **Page numbers** at bottom-left and bottom-right corners (e.g., "24" left, "26" right = previous/next page)

### Power Rails (Top of Page)
- **3-phase rails** L11/L12/L13 (or L21/L22/L23) as horizontal lines at the top
- Each rail has **cross-reference** showing origin page: `10.8 / -L11` (page 10.8, rail L11)
- Rails continue to the right edge with **destination cross-ref**: `-L11 / 26.0`
- **DC rails** below: `15F2.1` (24VDC), `0V` with their origin references
- Rails are **colored**: red for power, blue for DC/control (in this document style)

### Component Representation
- **Tag format**: `-36Q1`, `-27Q1`, `-25Q1` (location prefix + component type + number)
- **Specs below tag**: rating (6.4A), range (5.5...8A), part number (3RV2901-1E)
- **Cross-references**: `/53.1` next to a contactor means its coil/contacts are on page 53, column 1
- **Terminal numbers**: clearly labeled on each side (1,3,5 top / 2,4,6 bottom for 3-pole)
- **Contact mirrors**: auxiliary contacts shown with terminal pairs (13-14, 21-22)

### VFD/Drive Representation (Page 3 — SEW Eurodrive)
- Full VFD block showing:
  - All connector terminals (X1: L1-L3-PE, X2: U-V-W-PE motor, X4: DC link, X13: digital I/O)
  - Part number (MDX61B-0055-5A3-4-00)
  - Communication bus connector (fieldbus slot DFP21B)
  - Address DIP switch representation
  - Manufacturer logo (SEW EURODRIVE)
  - Shielding connections

### Soft Starter Representation (Page 4)
- Block symbol with:
  - Input terminals L1/L2/L3 top
  - Output terminals T1/T2/T3 bottom
  - Control terminals A1/A2, 14
  - Part number (SIE.3RW3047-1BB04)
  - "Sanftstarter / soft starter" label
  - Power specs (55kW, 100A)

### Cable Blocks
- Detailed cable info in dashed-line boxes:
  - Cable tag: `+ANL-36W1`
  - Type: `ÖLFLEX 110`
  - Spec: `4x1,5 mm²`
  - Gland: `M20x1,5`
  - Length: `27 m`
  - Wire colors at terminals: numbered (1, 2, 3) + GNYE for earth
- Cables show as dashed lines connecting terminal strips to motors

### Terminal Strips
- Tag: `XK`, `-XS`
- Individual terminals numbered: `○49 ○50 ○51 ○PE`
- Connection markers showing incoming/outgoing wires
- Terminal strips serve as breakpoints between field and cabinet wiring

### Motors (Bottom of Page)
- Circle symbol with M and 3~ inside
- Specs listed to the left:
  - Tag: `+ANL-36M1`
  - Voltage/Freq: `400V/50Hz`
  - Power: `3KW`
  - Current: `6,4A`
- PE connection shown separately
- **Function description** below in both German and English:
  - "Presse drehen / press rotate"
  - "Hydraulikpumpe / hydraulic pump"

### Title Block (Bottom)
Professional multi-cell title block:
```
┌──────────┬──────────┬─────────────┬───────────────┬──────────────────┬──────────┬──────────┐
│          │ Date     │ 14.08.2023  │ Kom-Nr: 212.999│                  │TORNADO   │ S.-Nr:   │
│          │ Drafted  │ a.juraschek │ Customer:      │ PRINZING PFEIFFER│150/150   │ 1238     │
│          │ Approved │             │ TRACEY CONCRETE│ (logo)           │Antriebe  │          │
├──────────┼──────────┼─────────────┤ LTD            │                  │Presse    │ Drawing: │
│Amendment │ Date     │ Name        │ Replaced by    │ Replaced by      │drives    │117052959 │
│          │          │ Original    │                │                  │press     │Page: 36  │
└──────────┴──────────┴─────────────┴───────────────┴──────────────────┴──────────┴──────────┘
```

Fields include:
- Date, Drafted by, Approved by
- Kom-Nr (commission number), Customer
- Company logo
- Machine: TORNADO 150/150
- Section description (bilingual DE/EN)
- S.-Nr (serial number)
- Drawing number
- Location references (= TOR, + S1)
- Page X of 383
- Amendment history rows

## Visual Style

### Colors
- **Component outlines**: Dark blue/navy
- **Component tags**: Dark blue, bold
- **Power rails (AC)**: Red
- **Wires**: Blue (control), Red (power)
- **Cross-references**: Red/orange for navigation refs
- **Terminal numbers**: Green/teal small text
- **Cable info**: Blue text
- **Earth/PE**: Standard earth symbol
- **Background**: White
- **Grid lines**: Not visible (clean white background)

### Typography
- Component tags: Bold, ~10pt
- Specs/ratings: Regular, ~7pt
- Terminal numbers: Small, ~6pt
- Cross-references: Italic or colored, ~7pt
- Function descriptions: Regular, ~9pt, bilingual

### Wire Style
- Horizontal and vertical only (orthogonal)
- Connection dots at junctions
- Dashed lines for cables between terminal strips and field devices
- Wire numbers shown where relevant

## Key Takeaways for AnlageX

1. **Cross-reference system is essential** — every component shows where its related parts are
2. **Cable blocks** are a major visual element in industrial schematics
3. **Terminal strips** serve as the boundary between cabinet and field
4. **Bilingual descriptions** are standard in international projects
5. **Title block** is much more detailed than what we currently have
6. **No row letters** — EPLAN uses column numbers + vertical position (different from QET's A-H rows)
7. **Component specs** (part numbers, ratings) are shown inline, not in a separate BOM
8. **Wire colors** are labeled at cable endpoints
9. **Page cross-references** (arrows with page.column) are critical for navigation
10. **Multiple circuits per page** — typically 2-5 motor circuits per power distribution page

---

*Source: Prinzing Pfeiffer TORNADO 150/150 — Drawing 117052959*
*Analyzed: 2026-02-23*
