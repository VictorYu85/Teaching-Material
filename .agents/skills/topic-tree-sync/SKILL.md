---
name: topic-tree-sync
description: Automatically scans the topics/ directory for HTML lesson files, extracts metadata comments, and synchronizes topic links, family clusters, and dependency arrows in index.html and progress.md. Use when user drops new HTML files into topics/ or asks to "sync topics", "update graph", or "refresh topic tree".
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

## Execution Workflow

When the user adds a new HTML file or asks to update the topic tree, execute these steps:

### Step 1: Scan and Parse Topics
1. Run a directory listing on `topics/*.html`.
2. For each file, parse the `<!-- Topic Metadata: ... -->` block to extract:
   - `id`: Unique identifier
   - `title`: Display title
   - `family`: Cluster name (e.g. `Valuation`, `Leasing`)
   - `prerequisites`: Array of parent topic IDs
   - `summary`: Short summary string
   - `file`: Path relative to root (e.g. `topics/debt-yield.html`)

### Step 2: Compute Layout Coordinates
Assign coordinates `(x, y)` to avoid node overlap and maintain natural left-to-right flow:
- Foundational topics (0 prerequisites): placed on column 1 (`x: 160`).
- 1st-level dependents: placed on column 2 (`x: 520`).
- 2nd-level dependents: placed on column 3 (`x: 880`).
- 3rd-level dependents: placed on column 4 (`x: 1200`).
- Vertical spacing: `y` increments by ~120px–180px per topic within its family cluster.
- Update the family's `CLUSTERS` bounding box `(x, y, width, height)` to enclose all nodes in that family.

### Step 3: Update `index.html`
Replace the `TOPICS` array and `CLUSTERS` array inside `index.html` with the freshly parsed and positioned topic definitions.

### Step 4: Update `progress.md`
Ensure `progress.md` has a checklist entry for the new topic under the correct family heading:
```markdown
- [ ] **[Topic Title]** (`topics/[filename].html`) — *Prerequisites: [list or "Foundational"]*
```
Preserve existing checked status (`[x]`) for all previously mastered topics!

---

## Checkable Completion Criteria
- [ ] Every `.html` file in `topics/` has an entry in `TOPICS` in `index.html`.
- [ ] All prerequisite edges render valid SVG arrows without broken references.
- [ ] `progress.md` lists every topic under its appropriate family heading.
- [ ] No previously marked `[x]` checkmarks in `progress.md` were overwritten.
