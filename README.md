# Teaching Material: Real Estate Learning Hub & Interactive Topic Tree

An interactive, visual learning system featuring a **2D pannable and zoomable canvas graph** (Obsidian Canvas / Figma style), split-screen lesson reader, dependency tracking, live `progress.md` synchronization via the File System Access API, and 12 real estate topic lessons formatted with non-breaking monospace keycap definitions.

---

## Quick Start

1. Open [`index.html`](index.html) in any modern browser (Chrome, Edge, Brave).
2. **Pan & Zoom**: Click and drag to pan across the canvas; scroll wheel or pinch to zoom in and out.
3. **Read Lessons**: Click any topic card on the graph to load the lesson into the right split-screen reader.
4. **Track Progress**: Click **"Mark Mastered"** on any topic to update your progress bar and unlock downstream prerequisites.
5. **Sync with `progress.md`**: Click the **"📁 Link progress.md"** button in the header and select `progress.md` on your computer. After linking once, your mastery progress writes directly to disk!

---

## Topic Architecture

```
Teaching-Material/
├── index.html                   # 2D Canvas Graph & Split-Screen Hub
├── progress.md                  # Plain-text checklist tracking mastery
├── topics/                      # Modular HTML lesson files
│   ├── cash-flow.html           # [Valuation] Foundational dependency
│   ├── real-estate-valuations.html # [Valuation] Foundational dependency
│   ├── cap-rate.html            # [Valuation] Depends on: cash-flow, real-estate-valuations
│   ├── rent-roll.html           # [Valuation] Depends on: cash-flow
│   ├── third-party-appraising.html # [Valuation] Depends on: real-estate-valuations, cap-rate
│   ├── irr.html                 # [Valuation] Depends on: cash-flow, cap-rate
│   ├── leasing-overview.html    # [Leasing] Foundational leasing topic
│   ├── property-taxes.html      # [Leasing] Depends on: leasing-overview, real-estate-valuations
│   ├── building-usefulness.html # [Leasing] Depends on: leasing-overview
│   ├── customer-preferences.html # [Leasing] Depends on: building-usefulness
│   ├── tenant-improvements.html # [Leasing] Depends on: leasing-overview, cash-flow
│   └── lease-timing-requirements.html # [Leasing] Depends on: customer-preferences, tenant-improvements
└── .agents/skills/
    ├── inline-collapsible-definitions/ # Monospace keycap design spec
    └── topic-tree-sync/         # AI skill to auto-update graph when new HTML files are dropped
```

---

## Adding New Topics with AI

Whenever you drop a new `.html` file into the `topics/` folder, include a metadata comment in the `<head>`:

```html
<!-- Topic Metadata:
  title: Debt Yield
  family: Valuation & Underwriting
  id: debt-yield
  prerequisites: ["cash-flow", "cap-rate"]
  summary: Net operating income divided by total loan amount.
-->
```

Then tell your AI agent:
> *"I added a new topic HTML file to `topics/`. Please sync the topic tree."*

The [`topic-tree-sync`](.agents/skills/topic-tree-sync/SKILL.md) skill will automatically parse your new topic, calculate its position on the canvas, draw the dependency arrows, and update `index.html` and `progress.md`.
