# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a dashboards repository containing visual tools for team planning and tracking. The primary dashboard is a sprint calendar visualization for 2026.

## Repository Structure

```
dashboards/
├── docs/
│   ├── sprints-2026.html             # Sprint calendar visualization (main)
│   └── sprints-2026-confluence.html  # Confluence-embedded variant
├── static/
│   ├── palette-colors.json           # TradingView design system color tokens
│   └── holidays.json                 # Source holiday data for 4 offices
├── CLAUDE.md
└── README.md
```

## Technology Stack

- **Frontend**: React 18 (loaded via CDN)
- **Styling**: Tailwind CSS (loaded via CDN)
- **Transpilation**: Babel Standalone (for JSX in browser)
- **Theming**: CSS Custom Properties (dark/light, follows system preference)
- **Language**: English (UI labels)

## Sprint Calendar (docs/sprints-2026.html)

A self-contained HTML file (~770 lines) that displays an interactive sprint calendar heatmap for 2026.

### Key Features
- Heatmap visualization of the full year
- 7 unique color shades per quarter (one per sprint, light→dark)
- Multi-office holiday overlay via CSS `linear-gradient` horizontal stripes
- Interactive office selector cell (4 stripes = 4 offices, clickable toggles)
- Selector acts as live legend — stripe colors reflect hovered day's holiday status
- Current sprint tracking with progress bar (calendar + working days)
- Click sprint in table or heatmap to highlight its days
- Tooltip with date, sprint, weekday, and per-office holiday status
- Dark/light theme support (follows system preference)
- Weekend rows hidden unless today is Saturday or Sunday

### Sprint Structure
- Sprints are 2-week periods (Monday to Friday of second week)
- Named as `26Q{quarter}S{sprint_number}` (e.g., 26Q1S1)
- Q1: 6 sprints, Q2: 7 sprints, Q3: 6 sprints, Q4: 7 sprints

### Color System
- All colors sourced from `static/palette-colors.json`
- `quarterColors.shades` = 7 levels (palette 200→800) per quarter
- Sprint color = `shades[sprintIndex]` (ordinal position within quarter)
- `current` = palette level 900 (today/selected highlight)
- `border` = palette level 700-800 (sprint separator lines)

### Holiday System
- `holidays.json` — source data (365 entries, 4 offices: CY, ES, GB, GE)
- `holidayMap` in HTML — extracted "Red" (public holiday) entries only
- `allOffices` = ["CY", "ES", "GB", "GE"] — alphabetical order everywhere
- Holiday stripes rendered via CSS `linear-gradient(to bottom, ...)`

### Development Notes
- No build process required - all dependencies loaded via CDN
- JSX is transpiled in-browser using Babel
- To test, simply open the HTML file in a browser
- Sprint data is hardcoded in the `sprintData` array (line ~58)
- Quarter colors are in `quarterColors` object (line ~87)
- Holiday data is in `holidayMap` object (line ~126)
- Layout constants: `CELL_SIZE=16`, `CELL_GAP=2`, `CELL_STEP=18` (line ~184)
