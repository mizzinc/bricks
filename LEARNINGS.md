# LEARNINGS.md
# Session corrections and refinements discovered through building
# Review periodically — promote stable rules into RULES.md
# Last updated: March 2026

---

## TYPOGRAPHY

### Don't declare what ACSS already owns on headings
- `font-weight` — omit unless overriding to a value different from `--heading-font-weight`
- `font-family` — omit unless overriding the global set in ACSS dashboard
- These are globally applied by ACSS to `h1`–`h6`. Restating them is redundant.

### Text content must be sentence case
- Never write uppercase text in HTML (`TECHNICALLY PRECISE`)
- Always use `text-transform: uppercase` in CSS
- Uppercase in the DOM is a semantic issue — screen readers, search engines, and maintainability all suffer

### Font size adjustments stay tethered to the ACSS scale
- Don't use `clamp()` or raw values to go beyond the type scale
- Use `calc()` on the nearest ACSS token:
  ```css
  font-size: calc(var(--h3) * 1.2);
  font-size: calc(var(--h1) + 1rem);
  ```
- This keeps the value responsive to global type scale changes

### Text max-width uses `ch` not ACSS width tokens
- `max-width: 40ch` is a content-driven constraint for text blocks
- `var(--width-xl)` is viewport-relative and not ideal for limiting text measure

---

## COLOUR & BACKGROUNDS

### `selection--alt` pairs with dark backgrounds
Promoted to RULES.md Section 5 — March 2026.

---

## SEMANTIC HTML

### Card grids use `<ul>` + `<li>`, not `<div>` + `<div>`
Promoted to RULES.md Section 10 — March 2026.

### Simple arrows/indicators use text characters, not inline SVG
Promoted to RULES.md Section 10 — March 2026.

---

## BRICKS STRUCTURE

### Content must be wrapped in containers
Promoted to RULES.md Section 10 — March 2026.

### Container `row-gap` must be declared explicitly
Promoted to RULES.md Section 6 — March 2026.

---

## BRICKS ELEMENT DEFAULTS
Promoted to RULES.md Section 2 — March 2026.

---

## CSS PATTERNS

### Don't declare card padding or gap when ACSS Card Framework is active
- Once a card class is added to Manual Card Selectors, ACSS applies `padding: var(--card-padding)` and `gap: var(--card-gap)` automatically
- Writing these in component CSS creates redundancy and potential specificity conflicts
- Only declare padding/gap on cards when deliberately overriding the framework value

### Read the design — check column ratios against ACSS grid tokens
- Don't default to `var(--grid-2)` (equal columns) when the design shows asymmetric splits
- Check available fractional tokens: `--grid-1-2`, `--grid-1-3`, `--grid-2-1`, `--grid-3-1`, `--grid-2-3`, `--grid-3-2`
- Match the visual weight ratio to the closest token

### `align-items` vs `justify-content` — check the axis
- `justify-content: flex-end` pushes children along the main axis (inline by default)
- `align-items: flex-end` aligns children along the cross axis (block by default)
- For a footer CTA right-aligned at the bottom of a flex column, `align-items: flex-end` is correct

### Bricks link elements need specificity handling
Promoted to RULES.md Section 2 — March 2026.

### Prefer single ACSS tokens over fluid expressions
- Use `var(--h3)` not `clamp(var(--h2), 5vw, 6rem)` unless the design genuinely requires fluid scaling beyond the ACSS scale
- If adjustment is needed, use `calc()` on the token (see Typography section above)

---

## CARDS — CLICKABLE PARENT

### Use ACSS `clickable-parent` for full-card click areas
Promoted to RULES.md Section 5 — March 2026.

---

## CONTENT GRID

### Content Grid is opt-in, never automatic
- "Default Sections to Content Grid" is OFF — do not assume sections are content grids
- Default structure remains: `section → container → content`
- Content Grid is used in specific cases only, e.g. single blog post templates where media needs to break wider than text
- When Content Grid IS used, apply `.content-grid` to the relevant container
- Children slot into the content zone by default; use `.content--feature`, `.content--feature-max`, `.content--full`, or `.content--full-safe` to break out
- Programmatic breakout via `grid-column: feature | feature-max | full` in BEM CSS
- Content Grid replaces the need for containers — the grid itself handles content-width and gutter
- Docs: https://docs.automaticcss.com/3.0/grids/content-grid

---

## BRICKS JSON

### Element IDs must be 6-character lowercase alphanumeric
Promoted to RULES.md Section 3 — March 2026.

### `%root%` does NOT work in `_cssCustom`
Promoted to RULES.md Section 3 — March 2026.

### Element labels — keep them plain
Promoted to RULES.md Section 3 — March 2026.

### `adminNotes` — use for FLAGS, not labels
Promoted to RULES.md Section 3 — March 2026.

### Button — use `text-link` with ACSS classes, not `button` element
Promoted to RULES.md Section 2 — March 2026.

---

## ANIMATIONS

### `_interactions` + `brx-animate` — confirmed pattern from live JSON

**`brx-animate` is NOT auto-applied by Bricks.** It must be explicitly added to `_cssGlobalClasses` on every animated element, alongside the BEM class. Confirmed from live import:

```json
"_cssGlobalClasses": ["bt1hdng", "vcvrjs"],
"_interactions": [
  {"id": "k7mx2p", "trigger": "enterView", "action": "startAnimation", "animationType": "fadeInUp"}
]
```

- `vcvrjs` is the live project ID for the `brx-animate` global class — always include it on animated elements
- `_interactions` and `_cssGlobalClasses: [..., "vcvrjs"]` always appear together on the same element

### Stagger pattern — confirmed from live JSON

`brx-animate--stagger-delay` CAN and SHOULD be added via `_cssGlobalClasses` in JSON output. It registers as its own global class object with empty settings:

```json
{"id": "cavsdm", "name": "brx-animate--stagger-delay", "settings": {}}
```

Applied to the content block alongside the BEM class:
```json
"_cssGlobalClasses": ["bt1cont", "cavsdm"]
```

- Parent gets `brx-animate--stagger-delay` in `_cssGlobalClasses` — no `_interactions` on the parent
- Each child gets `_interactions` + `vcvrjs` (`brx-animate`) in `_cssGlobalClasses`
- Do NOT set Duration or Delay in the panel for stagger children

### Interaction IDs
Promoted to RULES.md Section 3 (merged into rule 7) — March 2026.

---

*End of LEARNINGS.md*
