---
name: topic-tree-sync
description: Automatically scans the topics/ directory for HTML lesson files, extracts metadata comments, and synchronizes topic links, family clusters, and downward dependency arrows in index.html and progress.md. Use when user drops new HTML files into topics/ or asks to "sync topics", "update graph", or "refresh topic tree".
---

# Topic Tree Sync Skill

This skill synchronizes the 2D Canvas Graph (`index.html`) and the learning progress checklist (`progress.md`) whenever new `.html` lesson files are added, modified, or removed in the `topics/` directory.

---

## Topic HTML File Standards

Every topic HTML file in `topics/` must include a standard metadata comment block in its `<head>`:

```html
<!-- Topic Metadata:
  title: [Human Readable Title, e.g. "Debt Yield"]
  family: [Valuation | Leasing | Acquisitions | Due Diligence]
  id: [kebab-case-unique-id, e.g. "debt-yield"]
  prerequisites: ["cash-flow", "cap-rate"]
  summary: [1-sentence description of the concept]
-->
```

---

## Top-to-Bottom Layout Algorithm

The curriculum flows **top-to-bottom**:
- **Level 1 (Top / y: 110)**: Foundational topics (0 prerequisites).
- **Level 2 (Middle / y: 310)**: 1st-level dependents.
- **Level 3 (Lower / y: 520)**: 2nd-level dependents.
- **Level 4 (Bottom / y: 720)**: Final delivery / execution milestones.

### Connector Lines (Vertical Downward Bezier Curves)
Arrows connect from the **bottom center** of the parent card to the **top center** of the child card:
```javascript
const startX = parent.x + (CARD_WIDTH / 2);
const startY = parent.y + CARD_HEIGHT;
const endX = child.x + (CARD_WIDTH / 2);
const endY = child.y - 4;
```

---

## Execution Workflow

When the user adds a new HTML file or asks to update the topic tree, execute these steps:

### Step 1: Scan and Parse Topics
1. Run a directory listing on `topics/*.html`.
2. Parse the `<!-- Topic Metadata: ... -->` block for each file (`id`, `title`, `family`, `prerequisites`, `summary`).

### Step 2: Compute Top-to-Bottom Coordinates
Calculate `level = max(parent.level) + 1`. Place nodes in the appropriate family column and level row `y = 110 + (level - 1) * 200`.

### Step 3: Update `index.html` & `progress.md`
Update the `TOPICS` array in `index.html` and append/update entries in `progress.md` while preserving all existing `[x]` completion marks.
