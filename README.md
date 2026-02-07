# Sprint Calendar 2026

Interactive heatmap dashboard for tracking 26 two-week sprints across 4 quarters.

**Live:** open `docs/sprints-2026.html` in any browser (no build required).

---

## Repository Structure

```
dashboards/
├── docs/
│   ├── sprints-2026.html             # Main dashboard (self-contained)
│   └── sprints-2026-confluence.html  # Confluence-embedded variant
├── static/
│   ├── palette-colors.json           # TradingView design system colors
│   └── holidays.json                 # Source data: holidays for 4 offices (365 entries)
├── CLAUDE.md                         # AI assistant instructions
└── README.md
```

## Technology Stack

| Layer | Technology | Delivery |
|-------|-----------|----------|
| UI framework | React 18 | CDN (cdnjs) |
| Styling | Tailwind CSS | CDN |
| JSX transpilation | Babel Standalone 7 | CDN |
| Theming | CSS Custom Properties | Inline `<style>` |

No build step, no `node_modules`, no bundler.

---

## Architecture of `sprints-2026.html`

The file is a single HTML page (~770 lines) containing everything inline:

### 1. CSS Custom Properties (lines 11-50)

Two theme sets toggled via class `.light` on `<html>`:

| Variable | Purpose |
|----------|---------|
| `--bg` | Page background |
| `--card-bg` | Card background (semi-transparent) |
| `--card-border` | Card border |
| `--text` | Primary text |
| `--text-muted` | Secondary text |
| `--text-dim` | Tertiary text |
| `--hover-bg` | Hover highlight |
| `--progress-bg` | Progress bar track |
| `--non-sprint-bg` | Days outside any sprint |
| `--weekend-bg` | Weekend cells and holiday stripes |
| `--ring-offset` | Today cell ring offset |

Theme follows `prefers-color-scheme` and reacts to system changes in real time.

### 2. Data Constants (lines 58-186)

#### `sprintData` — 26 sprint definitions
```js
{ sprint: "26Q1S1", start: "2026-01-05", end: "2026-01-16", quarter: "Q1" }
```
- 2-week periods, Monday to Friday
- Naming: `26Q{quarter}S{number}` (e.g. 26Q3S5)
- Q1: 6 sprints, Q2: 7, Q3: 6, Q4: 7

#### `quarterColors` — color palette per quarter
```js
Q1: {
  shades: ["#90BFF9", ..., "#143EB2"],  // 7 levels, light→dark
  current: "#0C3299",                    // Today / selected sprint highlight
  border: "#143EB2",                     // Sprint separator lines
}
```

Colors sourced from `static/palette-colors.json`:

| Quarter | Palette family | Shade range |
|---------|---------------|-------------|
| Q1 | `tv-blue` | 200 → 800 |
| Q2 | `forest-green` | 200 → 800 |
| Q3 | `tan-orange` | 200 → 800 |
| Q4 | `berry-pink` | 200 → 800 |

Each sprint gets a unique shade by its ordinal index within the quarter:
`shades[0]` = S1 (lightest) → `shades[6]` = S7 (darkest).
`current` = one step darker than darkest shade (level 900).

#### `holidayMap` — embedded holiday data
Extracted from `holidays.json`. Only "Red" (public holiday) entries, keyed by date:
```js
"2026-01-06": { CY: true, ES: true }
```
4 offices: CY (Cyprus), ES (Spain), GB (Great Britain), GE (Georgia).

#### Layout constants
```js
const CELL_SIZE = 16;   // px per cell
const CELL_GAP = 2;     // px gap between cells
const CELL_STEP = 18;   // CELL_SIZE + CELL_GAP
```

### 3. React Component: `SprintHeatmap` (lines 199-764)

Single component, all state local:

| State | Type | Purpose |
|-------|------|---------|
| `hoveredDay` | object \| null | Day under cursor (drives tooltip + selector) |
| `selectedSprint` | string \| null | Sprint name clicked in table or heatmap |
| `selectedOffices` | string[] | Active office filters (alphabetical) |
| `theme` | "dark" \| "light" | Current theme |

#### Key computed values (useMemo)

- **`weeks`** — 2D array [week][day], each day:
  ```js
  { date, dateStr, sprint, isInYear, isToday, dayOfWeek, month }
  ```
- **`currentSprint`** — sprint object for today
- **`quarterProgress`** — progress stats for current quarter
- **`todayDay`** — day object for today (selector fallback)
- **`showWeekends`** — true only if today is Sat/Sun
- **`monthLabels`** — month label positions for header

#### Key functions

| Function | Returns | Purpose |
|----------|---------|---------|
| `getBackground(day)` | CSS `background` | Solid color or `linear-gradient` with holiday stripes |
| `isDayHoliday(day)` | boolean | Any selected office has a holiday |
| `isDayFullHoliday(day)` | boolean | All selected offices have a holiday |
| `getWeekSprintStart(week)` | sprint \| null | Detects sprint boundary for separator lines |
| `countWorkingDays(start, end)` | number | Mon-Fri count (excludes weekends) |

#### Color assignment logic (`getBackground`)

```
day is outside year     → transparent
day is weekend (Sat/Sun) → var(--weekend-bg)
day has no sprint       → var(--non-sprint-bg)
day is today OR selected → quarterColors[quarter].current
otherwise               → quarterColors[quarter].shades[sprintIndex]
```

When offices are selected, holiday overlay is applied via `linear-gradient`:
each selected office contributes one horizontal stripe (sprint color or `--weekend-bg`).

### 4. UI Layout

```
┌──────────────────────────────────────────────────┐
│ Today    Current Sprint   [Q1 progress bar]  42% │  ← Status panel
└──────────────────────────────────────────────────┘

🟦🟦🟦 Q1  🟩🟩🟩 Q2  🟧🟧🟧 Q3  🟪🟪🟪 Q4  ⬜ Out  ← Legend

┌──────────────────────────────────────────────────┐
│ Holidays  Jan  Feb  Mar  Apr ... Dec             │  ← Month labels
│ ┌──┐ Mon  ████████████████████████               │
│ │CY│ Tue  ████████████████████████               │  ← Office selector
│ │ES│ Wed  ████████████████████████               │    (clickable cell)
│ │GB│ Thu  ████████████████████████               │
│ │GE│ Fri  ████████████████████████               │
│ └──┘ Sat  ░░░░░░░░░░░░░░░░░░░░░░               │  ← Hidden unless
│      Sun  ░░░░░░░░░░░░░░░░░░░░░░               │    today is Sat/Sun
└──────────────────────────────────────────────────┘

┌─────────┬─────────┬─────────┬─────────┐
│   Q1    │   Q2    │   Q3    │   Q4    │  ← Sprint table
│ 26Q1S1  │ 26Q2S1  │ 26Q3S1  │ 26Q4S1  │    (click to select)
│ 26Q1S2  │ 26Q2S2  │ 26Q3S2  │ 26Q4S2  │
│ ...     │ ...     │ ...     │ ...     │
└─────────┴─────────┴─────────┴─────────┘
```

### 5. Office Selector (Holiday Legend)

A 70×70px square cell divided into 4 horizontal stripes (CY → ES → GB → GE, alphabetical).

- **Click** a stripe to toggle that office on/off
- **Active stripe** shows the sprint color of the hovered day (or today)
  - Holiday for that office → grey (`--weekend-bg`)
  - Working day → sprint shade color
- **Inactive stripe** → dimmed (`opacity: 0.35`, `--hover-bg`)
- Stripe color updates in real time as the cursor moves over the heatmap

### 6. Tooltip

Fixed at bottom-center. Shows on hover:
- Date, sprint name, weekday
- Holiday table (per selected office) when applicable

---

## Data Files

### `static/holidays.json`

Source data with 365 entries (one per day of 2026), 4 office columns.

Each entry:
```json
{
  "Date": "2026-01-06",
  "CY": { "value": null, "color": "Red", "background": "#ffbdad" },
  "GE": { "value": null, "color": "Teal", "background": "#b3f5ff" },
  ...
}
```

- `"Red"` = public holiday (non-working)
- `"Teal"` = weekend
- `null` / missing = regular working day

Only `"Red"` entries are extracted into `holidayMap` in the HTML.

### `static/palette-colors.json`

TradingView design system color tokens. Organized by family (e.g. `tv-blue`, `berry-pink`) with levels 50-900. Used as the source of truth for all quarter colors.

---

## How to Make Changes

### Change sprint dates or add sprints

Edit `sprintData` array (line ~58). Each entry:
```js
{ sprint: "26Q1S1", start: "2026-01-05", end: "2026-01-16", quarter: "Q1" }
```
Colors are assigned automatically by ordinal position within the quarter.

### Change quarter colors

1. Pick a color family from `static/palette-colors.json`
2. Choose 7 levels (200-800 recommended) for `shades`
3. Pick level 900 for `current`, level 700-800 for `border`
4. Update the corresponding entry in `quarterColors` (line ~87)

### Add a new office

1. Add entries to `holidays.json`
2. Add the office code to `allOffices` array (keep alphabetical)
3. Add flag and label to `officeFlags` and `officeLabels`
4. Extract holiday dates into `holidayMap`

### Change cell dimensions

Adjust constants (line ~184):
```js
const CELL_SIZE = 16;  // cell width/height in px
const CELL_GAP = 2;    // gap between cells
```
All layout calculations derive from these values.

### Change theme colors

Edit CSS custom properties in `<style>` (lines 17-44):
- `:root { ... }` — dark theme
- `:root.light { ... }` — light theme

### Add holidays from updated JSON

Re-extract "Red" entries from `holidays.json` into the `holidayMap` constant (line ~126).
Only dates with at least one `"Red"` office need an entry.

---

## Testing

Open `docs/sprints-2026.html` directly in a browser. No server needed (all CDN dependencies).

For local development with hot-reload, use any static file server:
```bash
cd docs && python3 -m http.server 8765
# open http://localhost:8765/sprints-2026.html
```
