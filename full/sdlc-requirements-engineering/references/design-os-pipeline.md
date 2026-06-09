# Design-OS Pipeline

10-command product design lifecycle (from earp-kit / Brian Casel / Builder Methods).

## Pipeline

```
Strategy:   product-vision → product-roadmap
Foundation: data-model → design-tokens
Structure:  design-shell → design-screen
Detail:     shape-section → sample-data
Output:     screenshot-design → export-product
```

## Stage Details

### Strategy
- **product-vision** — Define vision, target users, value proposition
- **product-roadmap** — Break vision into phases with prioritized features

### Foundation
- **data-model** — Define the data model backing the product (entities, relationships, constraints)
- **design-tokens** — Establish design tokens (colors, spacing, typography, shadows, border-radius)

### Structure
- **design-shell** — Create app shell (navigation, layout, chrome, responsive breakpoints)
- **design-screen** — Design individual screens within the shell

### Detail
- **shape-section** — Design individual sections/components within screens
- **sample-data** — Generate realistic sample data for all designed screens

### Output
- **screenshot-design** — Capture design screenshots for review
- **export-product** — Export complete product design (assets, specs, handoff docs)

## Key Insight
Product design is inherently sequential — can't design screens without a shell, can't design a shell without tokens. Pipeline enforces sequence while allowing skip/repeat.

## When to Use
- New product or major feature redesign
- Need structured design-to-code handoff
- Design system establishment

## When NOT to Use
- Bug fixes or minor UI tweaks
- Backend-only changes
- Copy changes or content updates
