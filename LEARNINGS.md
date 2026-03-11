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
- Always add `selection--alt` alongside `bg--dark` and `bg--ultra-dark`
- ACSS swaps selection colours via `--selection-bg-color-alt` and `--selection-text-color-alt`
- Without it, text selection is invisible or low-contrast on dark sections

---

## SEMANTIC HTML

### Card grids use `<ul>` + `<li>`, not `<div>` + `<div>`
- Any repeating set of cards/items is semantically a list
- Grid container → `<ul>` (Bricks `div` element, tag override to `ul`) with `list--none` utility class
- Each card → `<li>` (Bricks `div` element, tag override to `li`)
- This applies universally to portfolio grids, service cards, team grids, blog loops, etc.

### Simple arrows/indicators use text characters, not inline SVG
- A directional arrow like `↗` should be a `text-basic` element (`<span>`) with the Unicode character
- Reserve inline SVG for complex icons that have no Unicode equivalent
- Simpler, lighter, no SVG sourcing step required

---

## BRICKS STRUCTURE

### Content must be wrapped in containers
- Section children are `brxe-container` elements, not raw content blocks
- Related content groups (intro + grid + footer) share a container with explicit spacing
- Standalone elements (section kicker) get their own container if they need independent width/spacing behaviour
- The container provides `max-inline-size: var(--content-width)` and `margin-inline: auto`

### Container `row-gap` must be declared explicitly
- Don't assume ACSS contextual spacing will handle vertical gaps between content groups inside a container
- Set `row-gap: var(--container-gap)` on the `__wrap` container explicitly
- This spaces the intro, grid, and footer correctly

---

## BRICKS ELEMENT DEFAULTS

### `brxe-block` and `brxe-container` own `display: flex; flex-direction: column`
Bricks applies layout defaults via `@layer bricks` — confirmed from production DevTools:

```css
@layer bricks {
  .brxe-block {
    align-items: flex-start;
    display: flex;
    flex-direction: column;
    width: 100%;
  }
}

@layer bricks {
  .brxe-container {
    align-items: flex-start;
    display: flex;
    flex-direction: column;
    margin-left: auto;
    margin-right: auto;
    width: 1100px;
  }
}
```

**Never write `display: flex` or `flex-direction: column` on BEM classes applied to `block` or `container` elements.** Only write what you're overriding from the Bricks default:

```css
/* WRONG — Bricks already applies these */
%root%__content {
  display: flex;
  flex-direction: column;
  gap: var(--space-l);
}

/* CORRECT — gap is not a Bricks default, write it; display/direction are, omit them */
%root%__content {
  gap: var(--space-l);
}

/* CORRECT — changing direction IS an override, write it */
%root%__cta {
  flex-direction: row;
  gap: var(--space-s);
}
```

### `brxe-div` has no layout defaults
- Use `brxe-div` whenever you need `display: grid` or any layout that would fight against the block/container flex default
- Must declare `display: grid` (or `display: flex` for a non-column flex context) explicitly in BEM CSS

### Element selection guide
| Need | Use |
|---|---|
| Content group, column of text/buttons, flex column | `brxe-block` |
| Max-width section wrapper | `brxe-container` |
| CSS grid container | `brxe-div` |
| Flex row (non-column), icon group, tag list | `brxe-div` (declare `display: flex; flex-direction: row`) |
| Generic non-layout wrapper | `brxe-div` |

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
- Bricks adds `.brxe-text-link--btn` to text-link elements styled as buttons/CTAs
- Override Bricks link defaults by reassigning `--link-color` token AND including the Bricks modifier class in the selector for specificity:
  ```css
  %root%__cta-link.brxe-text-link--btn {
    --link-color: var(--text-dark-muted);
  }
  ```

### Prefer single ACSS tokens over fluid expressions
- Use `var(--h3)` not `clamp(var(--h2), 5vw, 6rem)` unless the design genuinely requires fluid scaling beyond the ACSS scale
- If adjustment is needed, use `calc()` on the token (see Typography section above)

---

## CARDS — CLICKABLE PARENT

### Use ACSS `clickable-parent` for full-card click areas
- ACSS provides `clickable-parent` — a utility class that stretches a link's click area over its parent container
- Apply `clickable-parent` to the `<a>` element inside the card, NOT to the card itself
- This makes the entire card clickable without wrapping everything in an `<a>` tag
- The link remains the semantic interactive element — better for accessibility and SEO
- Docs: https://docs.automaticcss.com/3.0/mixins/clickable-parent

```html
<!-- Correct: clickable-parent on the link inside the card -->
<li class="brxe-div bt-portfolio-card">
  <h3 class="brxe-heading bt-portfolio-card__heading">[Client name]</h3>
  <p class="brxe-text-basic bt-portfolio-card__tags">[Tags]</p>
  <a class="brxe-text-link bt-portfolio-card__url clickable-parent" href="#">clienturl.co.nz</a>
  <span class="brxe-text-basic bt-portfolio-card__arrow">↗</span>
</li>
```

- Always use `clickable-parent` on cards that link to a destination — don't write custom `::after` overlays or wrap entire cards in `<a>` tags
- The card needs `position: relative` for the overlay to work (ACSS card framework sets this automatically)

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
- Bricks expects exactly 6 characters, lowercase letters and numbers only
- Mixed formats like `pf0008a` (7 chars) will cause child elements to silently fail on import
- Use a consistent pattern: `pfab01`, `pfab02`, `pfac01`, etc.

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
- All `_interactions` entry IDs and element IDs: 6-char lowercase alphanumeric, randomised
- Never use obvious placeholders like `aaaaaa` or `xxxxxx` in final JSON output

---

## BRICKS JSON

### `%root%` does NOT work in `_cssCustom`
`%root%` is a Bricks Global Classes panel UI shorthand — it does not survive JSON import. Always use the hardcoded class name in `_cssCustom`:

```json
"_cssCustom": ".bt-hero {\n  min-height: 60vh;\n}\n.bt-hero__wrap {\n  flex-direction: row;\n}"
```

Never use `%root%` in authored JSON. Only use it when editing CSS directly inside the Bricks Global Classes panel.

### Element labels — keep them plain
Labels are for the Bricks structure panel only. Keep them short and clean:

```
"label": "Hero"
"label": "Wrap"
"label": "Content"
"label": "Heading"
"label": "Lead"
"label": "Media"
```

Never write FLAGs or instructions in labels. If something needs flagging, note it outside the JSON output.

### Button — use `text-link` with ACSS classes, not `button` element
Bricks wires ACSS button styles via `text-link` + global classes, not the native `button` element:

```json
{
  "name": "text-link",
  "settings": {
    "text": "Get started in 2 minutes",
    "link": {"type": "external", "url": "#", "rel": "nofollow", "ariaLabel": "...", "title": "..."},
    "_cssGlobalClasses": ["acss_import_btn--primary", "acss_import_btn--outline", "vcvrjs"],
    "_interactions": [{"id": "ngnbpi", "trigger": "enterView", "action": "startAnimation", "animationType": "fadeInUp"}]
  }
}
```

ACSS button global class IDs follow the pattern `acss_import_btn--[style]`.

---

*End of LEARNINGS.md*
