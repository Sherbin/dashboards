# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a dashboards repository containing visual tools for team planning and tracking. The primary dashboard is a sprint calendar visualization for 2026.

## Repository Structure

```
dashboards/
├── docs/
│   └── sprints-2026.html    # Sprint calendar visualization
└── README.md
```

## Technology Stack

- **Frontend**: React 18 (loaded via CDN)
- **Styling**: Tailwind CSS (loaded via CDN)
- **Transpilation**: Babel Standalone (for JSX in browser)
- **Language**: Russian (UI text and labels)

## Sprint Calendar (docs/sprints-2026.html)

A self-contained HTML file that displays an interactive sprint calendar heatmap for 2026.

### Key Features
- GitHub-style heatmap visualization of the year
- Quarter-based color coding (Q1=blue, Q2=green, Q3=orange, Q4=pink)
- Current sprint tracking with progress indicators
- Dark/light theme support (follows system preference)
- Responsive layout

### Sprint Structure
- Sprints are 2-week periods (Monday to Friday of second week)
- Named as `26Q{quarter}S{sprint_number}` (e.g., 26Q1S1)
- Q1: 6 sprints, Q2: 7 sprints, Q3: 6 sprints, Q4: 7 sprints

### Development Notes
- No build process required - all dependencies loaded via CDN
- JSX is transpiled in-browser using Babel
- To test, simply open the HTML file in a browser
- Sprint data is hardcoded in the `sprintData` array at the top of the component
