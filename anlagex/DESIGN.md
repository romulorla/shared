# AnlageX — Design Guide

## Theme System

Based on the AnlageAI Studio design system. Uses `oklch()` color space for perceptually uniform colors.
Tailwind CSS 4 + CSS custom properties. `next-themes` for dark/light toggle.

### Fonts

```css
--font-sans: 'Albert Sans', ui-sans-serif, sans-serif, system-ui;
--font-serif: 'Merriweather', serif;
--font-mono: 'Fira Code', monospace;
```

### Border Radius

```css
--radius: 0.3rem;
```

---

## Dark Theme (Primary — IDE default)

Warm dark tones. Background is deep brown-charcoal, not pure black. Primary accent is warm orange.

```css
.dark {
  /* Base */
  --background: oklch(0.2161 0.0061 56.0434);           /* Deep charcoal-brown */
  --foreground: oklch(0.9699 0.0013 106.4238);           /* Near-white, slightly warm */

  /* Cards & Popovers */
  --card: oklch(0.2685 0.0063 34.2976);                  /* Slightly lighter than bg */
  --card-foreground: oklch(0.9699 0.0013 106.4238);
  --popover: oklch(0.2685 0.0063 34.2976);
  --popover-foreground: oklch(0.9699 0.0013 106.4238);

  /* Primary (orange accent) */
  --primary: oklch(0.7049 0.1867 47.6044);               /* Warm orange */
  --primary-foreground: oklch(1.0000 0 0);                /* White */

  /* Secondary */
  --secondary: oklch(0.4444 0.0096 73.6390);             /* Muted warm gray */
  --secondary-foreground: oklch(0.9232 0.0026 48.7171);

  /* Muted */
  --muted: oklch(0.2330 0.0073 67.4563);                 /* Darker than card */
  --muted-foreground: oklch(0.7161 0.0091 56.2590);      /* Medium gray */

  /* Accent */
  --accent: oklch(0.3211 0 0);                           /* Neutral dark */
  --accent-foreground: oklch(0.9232 0.0026 48.7171);

  /* Destructive */
  --destructive: oklch(0.5771 0.2152 27.3250);           /* Red */
  --destructive-foreground: oklch(1.0000 0 0);

  /* Borders & Input */
  --border: oklch(0.3741 0.0087 67.5582);                /* Subtle warm border */
  --input: oklch(0.3741 0.0087 67.5582);
  --ring: oklch(0.7049 0.1867 47.6044);                  /* Orange focus ring */

  /* Charts */
  --chart-1: oklch(0.7049 0.1867 47.6044);               /* Orange */
  --chart-2: oklch(0.6847 0.1479 237.3225);               /* Blue */
  --chart-3: oklch(0.7952 0.1617 86.0468);                /* Yellow-green */
  --chart-4: oklch(0.7161 0.0091 56.2590);                /* Gray */
  --chart-5: oklch(0.5534 0.0116 58.0708);                /* Dark gray */

  /* Sidebar */
  --sidebar: oklch(0.2685 0.0063 34.2976);               /* Same as card */
  --sidebar-foreground: oklch(0.9699 0.0013 106.4238);
  --sidebar-primary: oklch(0.7049 0.1867 47.6044);
  --sidebar-primary-foreground: oklch(1.0000 0 0);
  --sidebar-accent: oklch(0.6847 0.1479 237.3225);       /* Blue accent */
  --sidebar-accent-foreground: oklch(0.2839 0.0734 254.5378);
  --sidebar-border: oklch(0.3741 0.0087 67.5582);
  --sidebar-ring: oklch(0.7049 0.1867 47.6044);

  /* Shadows */
  --shadow-color: hsl(0 0% 5%);
}
```

### Dark Theme Summary
| Role | Color | Description |
|------|-------|-------------|
| Background | `oklch(0.216 0.006 56)` | Deep charcoal with warm brown undertone |
| Card/Sidebar | `oklch(0.269 0.006 34)` | Slightly elevated surface |
| Muted | `oklch(0.233 0.007 67)` | Recessed areas, code blocks |
| Border | `oklch(0.374 0.009 68)` | Subtle warm borders |
| Primary | `oklch(0.705 0.187 48)` | **Warm orange** — buttons, links, focus |
| Foreground | `oklch(0.970 0.001 106)` | Near-white text |
| Muted text | `oklch(0.716 0.009 56)` | Secondary text, labels |

---

## Light Theme

Clean, warm light tones with the same orange primary.

```css
:root {
  /* Base */
  --background: oklch(0.9885 0.0057 84.5659);           /* Warm off-white */
  --foreground: oklch(0.3660 0.0251 49.6085);           /* Dark warm brown */

  /* Cards & Popovers */
  --card: oklch(0.9686 0.0091 78.2818);                 /* Slightly warm white */
  --card-foreground: oklch(0.3660 0.0251 49.6085);
  --popover: oklch(0.9686 0.0091 78.2818);
  --popover-foreground: oklch(0.3660 0.0251 49.6085);

  /* Primary (orange accent) */
  --primary: oklch(0.5553 0.1455 48.9975);              /* Darker orange for contrast */
  --primary-foreground: oklch(1.0000 0 0);

  /* Secondary */
  --secondary: oklch(0.8276 0.0752 74.4400);            /* Light warm tan */
  --secondary-foreground: oklch(0.4444 0.0096 73.6390);

  /* Muted */
  --muted: oklch(0.9363 0.0218 83.2637);                /* Light warm gray */
  --muted-foreground: oklch(0.5534 0.0116 58.0708);

  /* Accent */
  --accent: oklch(0.9000 0.0500 74.9889);               /* Light warm accent */
  --accent-foreground: oklch(0.4444 0.0096 73.6390);

  /* Destructive */
  --destructive: oklch(0.4437 0.1613 26.8994);
  --destructive-foreground: oklch(1.0000 0 0);

  /* Borders & Input */
  --border: oklch(0.8866 0.0404 89.6994);               /* Warm light border */
  --input: oklch(0.8866 0.0404 89.6994);
  --ring: oklch(0.5553 0.1455 48.9975);

  /* Sidebar */
  --sidebar: oklch(0.9363 0.0218 83.2637);
  --sidebar-foreground: oklch(0.3660 0.0251 49.6085);
  --sidebar-primary: oklch(0.5553 0.1455 48.9975);
  --sidebar-primary-foreground: oklch(1.0000 0 0);
  --sidebar-accent: oklch(0.5538 0.1207 66.4416);
  --sidebar-accent-foreground: oklch(1.0000 0 0);
  --sidebar-border: oklch(0.8866 0.0404 89.6994);
  --sidebar-ring: oklch(0.5553 0.1455 48.9975);

  /* Shadows */
  --shadow-color: hsl(28 18% 25%);
}
```

---

## AnlageX-Specific Design Tokens

Beyond the base theme, AnlageX needs tokens specific to a schematic IDE:

```css
/* Canvas */
--canvas-bg-dark: oklch(0.18 0.003 56);                /* Slightly darker than app bg */
--canvas-bg-light: oklch(1.0 0 0);                     /* Pure white for schematics */
--canvas-grid-dark: oklch(0.28 0.005 56);              /* Subtle grid lines */
--canvas-grid-light: oklch(0.92 0.005 84);
--canvas-grid-major-dark: oklch(0.35 0.008 56);        /* Major grid lines */
--canvas-grid-major-light: oklch(0.85 0.01 84);

/* Wires & Conductors */
--wire-color: oklch(0.0 0 0);                          /* Black in both themes */
--wire-selected: var(--primary);                        /* Orange when selected */
--wire-hover: oklch(0.5 0.15 240);                     /* Blue on hover */
--wire-width: 2px;

/* Terminals / Connection Points */
--terminal-color: oklch(0.4 0.15 145);                 /* Green dot */
--terminal-hover: oklch(0.6 0.2 145);                  /* Bright green on hover */
--terminal-size: 6px;

/* Selection */
--selection-fill: oklch(0.7049 0.1867 47.6044 / 0.15); /* Orange tint */
--selection-stroke: var(--primary);

/* Component Labels */
--label-tag: var(--primary);                           /* -Q1, -KM1 in orange */
--label-description: var(--muted-foreground);           /* Description in muted */
```

---

## Layout Zones

```
┌─────────────────────────────────────────────────────────┐
│ Toolbar (48px height)                    bg: card       │
├────────────┬──────────────────────┬─────────────────────┤
│ Component  │                      │ Chat / Properties   │
│ Tree       │  Canvas              │                     │
│            │                      │ bg: card            │
│ width:     │  bg: canvas-bg       │ width:              │
│ 240-300px  │  (fills remaining)   │ 300-400px           │
│ resizable  │                      │ collapsible         │
│            │                      │                     │
│ bg: sidebar│                      │                     │
├────────────┴──────────────────────┴─────────────────────┤
│ Status Bar (28px)                        bg: muted      │
└─────────────────────────────────────────────────────────┘
```

## Component Tree Style

- Background: `--sidebar`
- Hover: `--accent`
- Selected: `--sidebar-primary` with `--sidebar-primary-foreground`
- Category icons: Small monochrome SVG icons
- Indent guides: `--sidebar-border`
- Search input at top with `--input` border

## Canvas Style

- Background: `--canvas-bg` (dark: very dark, light: white)
- Grid: Dotted pattern matching QET/EPLAN style
- Components: Black strokes, no fill (IEC standard)
- Selected component: Orange highlight stroke + selection handles
- Wiring mode: Blue cursor, snap indicators on terminals (green dots)

## Chat Panel Style

- Background: `--card`
- User messages: `--primary` bg with white text (right-aligned)
- AI messages: `--muted` bg with `--foreground` text (left-aligned)
- Input: Bottom-pinned with `--input` border
- Collapsible via toggle button

---

*Source: AnlageAI Studio (`romulorla/anlageai-studio`) globals.css*
*Created: 2026-02-23*
