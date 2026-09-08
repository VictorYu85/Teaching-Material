---
name: inline-collapsible-definitions
description: Create seamless, non-breaking monospace keycap collapsible definitions and inline glossary tooltips that preserve continuous sentence flow. Use when user wants inline definitions, collapsible terms, interactive glossaries, Ben's Bites style pills, or mentions "nutshell" or "definition tooltip".
---

# Inline Collapsible Definitions

Inline collapsible definitions allow readers to inspect technical or financial terms without leaving their reading flow. 

When implemented poorly, browsers insert awkward line breaks before and after terms, breaking sentences into fragmented lines. This skill provides the battle-tested pattern for professional, **non-breaking monospace keycaps** with floating popovers or inline sentence expansions.

---

## The Critical Failure Mode: Why `<details>` Breaks Lines

Standard `<details>` elements are classified as disclosure widgets with native block formatting contexts in browser user-agent stylesheets. Even with `display: inline;` declared in CSS, Chromium and WebKit layout engines enforce line break boundaries around `<details>`.

**The Solution**: Always use native inline containers (`<span>`) with `<button type="button">` and an absolutely positioned popover or inline badge.

---

## Visual Design Spec: The Monospace Keycap

The monospace keycap provides a modern, executive "hardware keyboard" feel (similar to Stripe, Linear, and modern documentation portals) without looking cartoonish:

- **Font**: `ui-monospace, SFMono-Regular, "SF Mono", Menlo, Consolas, monospace`
- **Dimensions**: `padding: 1px 6px; font-size: 0.8125rem; font-weight: 600; line-height: 1.35;`
- **Border**: `1px solid #CBD5E1` with a `2px solid #94A3B8` base depth.
- **Background**: `#FFFFFF` (idle), `#F8FAFC` (hover), `#EFF6FF` (active).
- **Active Accent**: `#3B82F6` border with `#1D4ED8` text.
- **Indicator**: A subtle `9px` chevron (`›`) that rotates `90deg` on open.

---

## Pattern 1: Floating Popover (Recommended)

Keeps the sentence completely intact with **zero layout shift** or line movement. The definition floats above the term with a clean pointer caret.

### CSS

```css
/* Monospace Keycap Button */
.keycap {
  display: inline-flex;
  align-items: center;
  gap: 3px;
  padding: 1px 6px;
  margin: 0 1px;
  background: #FFFFFF;
  border: 1px solid #CBD5E1;
  border-bottom: 2px solid #94A3B8;
  border-radius: 5px;
  color: #334155;
  font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, monospace;
  font-size: 0.8125rem;
  font-weight: 600;
  cursor: pointer;
  vertical-align: baseline;
  line-height: 1.35;
  white-space: nowrap;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.04);
  transition: all 0.12s ease;
}
.keycap:hover {
  background: #F8FAFC;
  border-color: #94A3B8;
  color: #0F172A;
}
.keycap.active {
  background: #EFF6FF;
  border-color: #3B82F6;
  border-bottom: 2px solid #2563EB;
  color: #1D4ED8;
}
.keycap .arrow {
  font-size: 10px;
  opacity: 0.6;
  transition: transform 0.15s ease;
  display: inline-block;
}
.keycap.active .arrow {
  transform: rotate(90deg);
  opacity: 1;
}

/* Inline Positioning Anchor */
.popover-wrap {
  display: inline;
  position: relative;
}

/* Floating Card */
.popover-card {
  display: none;
  position: absolute;
  bottom: calc(100% + 9px);
  left: 50%;
  transform: translateX(-50%);
  width: 275px;
  background: #FFFFFF;
  border: 1px solid #CBD5E1;
  border-radius: 8px;
  padding: 12px 14px;
  box-shadow: 0 10px 25px -5px rgba(15, 23, 42, 0.15), 0 4px 6px -2px rgba(15, 23, 42, 0.05);
  font-size: 13px;
  line-height: 1.5;
  color: #334155;
  z-index: 100;
  text-align: left;
  white-space: normal;
}
.popover-card.show {
  display: block;
}
/* Popover Caret */
.popover-card::after {
  content: "";
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  border-width: 6px;
  border-style: solid;
  border-color: #FFFFFF transparent transparent transparent;
}
```

### HTML Usage

```html
<p>
  The acquisition anticipates an average monthly 
  <span class="popover-wrap">
    <button type="button" class="keycap" onclick="togglePopover(this)">
      <span>cash_flow</span>
      <span class="arrow">›</span>
    </button>
    <span class="popover-card">
      <strong style="color: #2563EB; font-family: monospace; display: block; margin-bottom: 4px;">cash_flow</strong>
      Net revenue remaining each month after settling all operating costs, maintenance reserves, and mortgage debt.
    </span>
  </span>
  of $3,800 alongside an initial return of 7.2%.
</p>
```

### JavaScript

```javascript
function togglePopover(btn) {
  const card = btn.nextElementSibling;
  const isCurrentlyOpen = card.classList.contains('show');

  // Close all other open popovers
  document.querySelectorAll('.popover-card').forEach(c => c.classList.remove('show'));
  document.querySelectorAll('.keycap').forEach(b => b.classList.remove('active'));

  if (!isCurrentlyOpen) {
    card.classList.add('show');
    btn.classList.add('active');
  }
}

// Click-outside dismissal
document.addEventListener('click', (e) => {
  if (!e.target.closest('.popover-wrap')) {
    document.querySelectorAll('.popover-card').forEach(c => c.classList.remove('show'));
    document.querySelectorAll('.keycap').forEach(b => b.classList.remove('active'));
  }
});
```

---

## Pattern 2: Continuous Inline Badge (Zero JS Optional)

When the definition must sit directly inside the sentence as flowing text rather than floating above it:

```html
<span class="inline-def-wrap">
  <button type="button" class="keycap" onclick="this.nextElementSibling.classList.toggle('show')">
    cap_rate ›
  </button>
  <span class="inline-badge">
    <strong>(Cap Rate:</strong> Net Operating Income / Purchase Price<strong>)</strong>
  </span>
</span>
```

Style the `.inline-badge` with `display: inline; background: #EFF6FF; border: 1px solid #BFDBFE; padding: 1px 6px; border-radius: 4px;` so it flows with surrounding words without forcing line breaks.

---

## Checkable Completion Criteria

When implementing collapsible inline definitions:
- [ ] No `<details>` elements are used inside running sentences.
- [ ] Surrounding text (`"monthly [keycap] of $3,800"`) stays on the exact same line without breaking.
- [ ] Keycaps use monospace font with clean 1px border and 2px base edge.
- [ ] Popovers close cleanly when the user clicks elsewhere on the page.
