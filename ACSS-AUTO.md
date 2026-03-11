# ACSS-AUTO.md — What ACSS Owns Automatically
## Reference for code generation — do NOT re-declare what ACSS already handles
Version: 1.2 | Based on ACSS 3.0 documentation

---

## THE CORE PRINCIPLE

ACSS is NOT utility-first. The correct workflow is:
- **Reusable components** → custom BEM classes + ACSS variables
- **One-off contextual overrides** → utility classes (`.bg--dark`, `section--xl`, etc.)
- **Never** write CSS that restates what ACSS already applies globally

**Variables are the real power.** Always prefer `var(--token)` in custom classes over utility classes for re-usable elements. Utility classes are appropriate for one-off contextual changes only.

---

## 1. TYPOGRAPHY — ACSS OWNS ALL OF THIS BY DEFAULT

Do NOT declare any of the following unless you are explicitly overriding a global default for a specific component.

### Global defaults applied to body/text automatically:
- `font-family` (set at body via `--text-font-family`)
- `font-size` (fluid responsive, scales from mobile to desktop)
- `color` (set via `--body-color` / `--text-dark`)
- `line-height` (Smart Line Height — a formula, not a static value)
- `font-weight`
- `letter-spacing`
- `text-wrap: pretty` (orphan prevention — default)

### Global defaults applied to all headings automatically:
- `font-family` (via `--heading-font-family`)
- `color` (via `--heading-color`)
- `line-height` (Smart Line Height)
- `font-weight`
- `letter-spacing`
- `text-transform`

### Font size variables available (do NOT hard-code sizes):
```
--h1, --h2, --h3, --h4, --h5, --h6
--text-xxl, --text-xl, --text-l, --text-m, --text-s, --text-xs
```

### When to write typography CSS:
- Element needs a size DIFFERENT from its semantic default → `font-size: var(--h4)` on an h2
- Element needs a font-family different from global → `font-family: var(--heading-font-family)`
- Element needs a color variant (e.g. muted text) → `color: var(--text-dark-muted)`
- Creating a deliberate style exception (e.g. a display heading)

---

## 2. SECTION SPACING — ACSS OWNS THIS

### What ACSS applies automatically:
- **Section block padding** — "M" section padding is applied to ALL top-level `<section>` elements by default
- **Section inline padding** (gutters) — fluid horizontal padding between content and viewport edge

### Do NOT write:
```css
/* WRONG — ACSS already handles this */
.bt-hero {
  padding-block: var(--section-space-m);
  padding-inline: var(--section-padding-x);
}
```

### When to write section padding CSS:
- You need MORE or LESS than M spacing → use the utility class `.section--xl`, `.section--s` etc. on the `<section>` element
- You need fine-tuning → `calc(var(--section-space-m) * 1.2)` on a custom class
- You are creating overlap effects using negative margin

### Section spacing variables (for calculations only):
```
--section-space-xs, --section-space-s, --section-space-m,
--section-space-l, --section-space-xl, --section-space-xxl
```

### Section padding utility classes:
```
.section--xs  .section--s  .section--m  .section--l  .section--xl  .section--xxl
```

---

## 3. CONTEXTUAL SPACING — ACSS OWNS THIS (if Auto Contextual Spacing is ON)

When Automatic Contextual Spacing is enabled (ACSS 2.6+):
- `section` elements automatically get `gap: var(--container-gap)` between direct child divs
- Direct child divs of `section` automatically get `gap: var(--content-gap)` between content elements
- Grid utility class elements automatically get `gap: var(--grid-gap)`

### Contextual spacing variables (use these in custom classes):
```
var(--container-gap)   — gap between containers in a section
var(--content-gap)     — gap between content elements (headings, text, etc.)
var(--grid-gap)        — gap between grid/column items
```

### When to write gap CSS:
- Custom layout that doesn't use standard section/grid structure
- Deliberate deviation from the global gap value → `gap: calc(var(--grid-gap) * 2)`
- A component needs a different internal gap

---

## 4. BUTTONS — ACSS OWNS ALL OF THIS

### What ACSS handles automatically when a btn class is present:
- Padding (Y and X, em-based, scales with text size)
- Minimum width
- Line-height
- Letter-spacing
- Font-weight
- Font-style
- Text-transform
- Text-decoration
- Border-width, border-style
- Border-radius (uses global `--radius` by default)
- Transition (uses global transition by default)
- Background color, text color, border color
- Hover states for all of the above
- Focus styling

### Button classes (use these, never recreate from scratch):
```
.btn--primary        .btn--primary.btn--outline
.btn--secondary      .btn--secondary.btn--outline
.btn--neutral        .btn--neutral.btn--outline
(+ light/dark variants for each)
```

### When to write button CSS:
- Overriding a single button instance via local token redefinition:
```css
#special-btn {
  --btn-background: var(--primary-dark);
  --btn-padding-inline: 4em;
}
```
- Creating a custom button style that follows `.btn--` naming:
```css
.btn--custom {
  /* define all --btn-* tokens here */
}
```

### NEVER write:
```css
/* WRONG — ACSS owns this */
.bt-hero__cta {
  padding: 0.75em 1.5em;
  background: var(--primary);
  color: white;
  border-radius: var(--radius);
  transition: 0.3s ease;
}
```

---

## 5. CARDS — ACSS OWNS THIS (if Card Framework is enabled)

### What ACSS applies automatically to any class containing "card":
- `padding: var(--card-padding)`
- `gap: var(--card-gap)`
- `border-radius: var(--card-radius)` (or concentric radius)
- `position: relative`
- `overflow: hidden`
- Background color (light or dark theme)
- Heading color, text color
- Border color and width
- Shadow
- Button style within card context

### Automatic targeting selector (default):
```css
:is([class*='card']:where(:not([class*='__'], [class*='wrapper'])))
```

> ⚠️ **Never assume auto card styling will fire.** Two things can silently break it:
> 1. "Style Cards Automatically" is OFF (default on existing projects) — enable in ACSS → Cards → Options
> 2. The auto card selector has been modified with an exclusion list — on established projects this list can be extensive. Your new card class may not be excluded, but a modified `:where()` structure can behave unexpectedly.
>
> **Always FLAG card classes in output** and instruct the developer to either confirm the class is matched by the auto selector, or add it explicitly to **Manual Card Selectors** in ACSS → Cards → Targeting Settings.
>
> Manual selector approach (safest on established projects):
> Add `.bt-[name]-card` to the Manual Card Selectors input — this bypasses the auto selector entirely.

### BEM is essential for cards:
```
✅ .article-card__heading  (child — not auto-styled)
❌ .article-card-heading   (no __ — ACSS will try to style this as a card)
```

### Card variables for manual override:
```
--card-padding, --card-gap, --card-radius
--card-heading-size, --card-text-size
```

### Card modifier classes:
```
.article-card--dark    (force dark style)
.article-card--light   (force light style)
.article-card--alt     (flip to opposite of default)
```

---

## 6. ICONS — ACSS OWNS THIS (if Icon Framework is enabled)

### What ACSS applies automatically when `data-icon` attribute is present:
- Icon color (light or dark theme)
- Icon size (default M)
- Padding (boxed style)
- Background color (boxed)
- Border color, border-radius (boxed)
- Box shadow (boxed)
- Hover states

### Usage:
```html
<svg data-icon xmlns="..." />
```

### Override classes:
```
.icon--light    .icon--dark
.icon--boxed    .icon--naked
.icon--s        .icon--m       .icon--l
```

### NEVER set icon size, color, or background manually — use the framework.

---

## 7. FORMS — ACSS OWNS THIS (if Form Styling is enabled)

ACSS applies automatic single-class form styling to standard HTML form elements. Do not re-style inputs, labels, selects, textareas, or buttons inside forms unless deliberately overriding.

---

## 8. BORDERS & RADIUS

### Do NOT hard-code radius values. Always use:
```
var(--radius)          — global border radius (use this everywhere)
var(--border)          — full border shorthand (width + style + color)
var(--border-light)    — light variant
var(--border-dark)     — dark variant
var(--border-size)     — border width only
var(--border-style)    — border style only
```

### Auto Radius:
ACSS can automatically apply `var(--radius)` to `img` and `figure` elements — check if this is active before adding radius to images manually.

### Border utility classes:
```
.border          .border--light          .border--dark
.radius          (applies global radius only)
```

---

## 9. COLORS & BACKGROUNDS

### Background assignments — use these, not specific color tokens:
```
.bg--light        var(--bg-light)
.bg--dark         var(--bg-dark)
.bg--ultra-light  var(--bg-ultra-light)
.bg--ultra-dark   var(--bg-ultra-dark)
```

**Important:** Use the utility CLASS (not the variable) when you want Automatic Color Relationships to trigger (foreground text/heading/button colors flip automatically).

### Text color assignments:
```
var(--text-light)         var(--text-dark)
var(--text-light-muted)   var(--text-dark-muted)
var(--body-color)         — default text color
var(--body-bg-color)      — default background
```

### Automatic Color Relationships:
When `.bg--ultra-dark` (etc.) is applied as a class, ACSS automatically changes heading color, text color, link color, and button style to match. This is ONLY triggered by the utility class, NOT by using the variable in custom CSS.

---

## 10. LAYOUT — FLEX AND GRID SYSTEMS

ACSS provides multiple layout systems. Always choose the right tool before writing any custom CSS.

---

### THE CORE RULE

**Use Flexbox** when you want a **flexible layout** — the layout adapts to fit the content. Flex works with the intrinsic sizing of items (items are as big as their content, then grow or shrink from there). The children influence the layout.

**Use Grid** when you want a **fixed layout** — the content adapts to fit the layout. Grid works against intrinsic sizing — the parent defines the tracks, children fill them regardless of content size. The parent controls everything.

This is more useful than "1D vs 2D." Both systems can produce two-dimensional layouts. The real difference is **where control lives and whether intrinsic sizing is an asset or a problem.**

**Mixing is correct and encouraged:**
- Grid for structural layout (columns, page architecture, card grids)
- Flex for components inside those grid cells (nav items, tag lists, button groups, icon rows)

**Warning signs you picked the wrong tool:**
- Writing `flex-basis`, `flex-shrink: 0`, or `min-width` to force flex children to hold specific widths → stop, use grid instead
- Writing CSS on children to override grid track sizing → stop, let the parent define it via `grid-template-columns`

### WHAT BRICKS OWNS (separate from ACSS)

Bricks applies layout defaults via `@layer bricks` — lower specificity than your BEM CSS, but still redundant to redeclare:

| Element | Bricks default |
|---|---|
| `brxe-block` | `display: flex; flex-direction: column; align-items: flex-start; width: 100%` |
| `brxe-container` | `display: flex; flex-direction: column; align-items: flex-start; margin-inline: auto` |
| `brxe-div` | No layout defaults |
| `brxe-section` | No layout defaults |

**Never write `display: flex` or `flex-direction: column` on BEM classes applied to `block` or `container` elements.** Bricks already does it. Only write overrides — what you want to *change* from the default:

```css
/* WRONG — Bricks already applies display:flex and flex-direction:column */
%root%__content {
  display: flex;
  flex-direction: column;
  gap: var(--space-l);
}

/* CORRECT — only override what differs from Bricks default */
%root%__content {
  gap: var(--space-l);
}

/* CORRECT — changing direction is an override, write it */
%root%__cta {
  flex-direction: row;
  gap: var(--space-s);
}
```

**Use `brxe-div` when you need `display: grid`** — no Bricks layout defaults to fight against.

---

### DECISION GUIDE

| Situation | System | How |
|---|---|---|
| Children should share or negotiate space | **Flexbox** | `flex: 1` on each child |
| Nav items, button groups, tag lists, icon rows | **Flexbox** | `display: flex` + `flex-wrap` |
| Items should wrap based on available space | **Flexbox** | `flex-wrap: wrap` |
| Parent should control track sizes rigidly | **Grid** | `var(--grid-N)` or `grid-template-columns` |
| Multi-column equal grid, fixed breakpoints | **Grid classes** | `.grid--3 .grid--l-2 .grid--m-1` |
| Multi-column equal grid, reusable BEM | **Grid variables** | `grid-template-columns: var(--grid-3)` |
| Auto-responsive, content-driven stacking | **Auto Grid** | `var(--grid-auto-3)` or `.grid--auto-3` |
| Auto-responsive, child-width-driven stacking | **Variable Grid** | `.variable-grid` + `--min: 280px` |
| Unbalanced items centred at bottom of grid | **Flex Grid** | `.flex-grid--3` (requires Flex Grid in ACSS options) |
| Alternating text/image rows | **Auto Alternating Grid** | `.grid--2 .grid-alternate--l` on wrapper |
| Uneven columns (1:2, 2:3 etc.) | **Grid fractional** | `.grid--1-2` or `var(--grid-1-2)` |
| Complex track control, named areas, spans | **Custom grid CSS** | Write `grid-template-columns` with FLAG |

---

### FLEXBOX — for flexible, content-driven layouts

Use when the layout should adapt to fit the content. Items rely on their intrinsic size and grow or shrink from there.

**When to write `display: flex`:**
- Only on `brxe-div` elements — `brxe-block` and `brxe-container` already have it from Bricks
- When a `div` element needs to become a flex row or flex container

**50/50 equal split — `__wrap` on a `brxe-div`:**
```css
/* __wrap is a brxe-div — must declare display:flex explicitly */
%root%__wrap {
  display: flex;
  align-items: center;
}
%root%__content,
%root%__media {
  flex: 1;
}
@media (max-width: 768px) {
  %root%__wrap {
    flex-direction: column;
    gap: var(--content-gap);
  }
}
```

**If `__wrap` were a `brxe-block` or `brxe-container` instead:**
```css
/* display:flex and flex-direction:column already applied by Bricks — omit both */
%root%__wrap {
  align-items: center; /* override from flex-start */
}
%root%__content,
%root%__media {
  flex: 1;
}
@media (max-width: 768px) {
  %root%__wrap {
    gap: var(--content-gap);
    /* flex-direction:column already the default — no need to write it */
  }
}
```

**Flex utility classes (apply to parent):**
```
.flex--row     .flex--col     .flex--row-reverse     .flex--col-reverse
.flex--wrap    .flex--grow
```

**Alignment utilities:**
```
.justify-content--[start|end|center|between|around|stretch]
.align-items--[start|end|center|baseline|stretch]
.align-content--[start|end|center|baseline|stretch]
.justify-items--[start|end|center|stretch]
.self--[start|end|center|stretch]
```

All flex utilities support breakpoint modifiers: `.flex--col-m`, `.align-items--start-l` etc.

---

### FLEX GRID — for unbalanced item centring

Use when you want grid items that don't fill a complete row to be **centred** rather than left-aligned. Requires Flex Grid to be enabled in ACSS options. No variables available — utility classes only.

```
.flex-grid--2  through  .flex-grid--6
.flex-grid--l-2  .flex-grid--m-1  (breakpoint modifiers)
.flex--grow  (on container — stretches unbalanced items instead of centring)
```

---

### STANDARD GRID CLASSES — for fixed-breakpoint multi-column grids

Apply to parent container. Full 12-column support. Use for one-off grids; use BEM + grid variables for reusable grids.

```
.grid--1  through  .grid--12
.grid--l-2  .grid--m-1  (breakpoint modifiers)
```

**Uneven/fractional classes:**
```
.grid--1-2  .grid--2-1  .grid--1-3  .grid--3-1  .grid--2-3  .grid--3-2
```

**Gap:** Use `.fr-grid-gap` (contextual) or `gap: var(--grid-gap)` in BEM CSS. Never use raw gap values.

**Equal height:** `.stretch` on parent forces all cells to match tallest cell height.

**Column/row control:**
```
.col-span--[1–12]    .col-span--all
.col-start--[1–12]   .col-end--[1–12]   .col-end--last
.row-span--[1–6]     .row-start--[1–4]  .row-end--[1–4]
.order--first        .order--last
.order--first-[bp]   .order--last-[bp]
```

---

### GRID VARIABLES — for reusable BEM grid classes

Preferred over utility classes for grids used on multiple pages. Use in custom BEM CSS, not as utility classes on elements.

```css
.bt-services__grid {
  display: grid;
  grid-template-columns: var(--grid-3);
  gap: var(--grid-gap);
}
@media (max-width: 992px) {
  .bt-services__grid {
    grid-template-columns: var(--grid-2);
  }
}
```

Available variables:
```
var(--grid-1) through var(--grid-12)
var(--grid-1-2)  var(--grid-2-1)  var(--grid-1-3)
var(--grid-3-1)  var(--grid-2-3)  var(--grid-3-2)
```

---

### AUTO GRID — automatically responsive, no breakpoint decisions

Cells stack automatically when squeezed to their minimum width. Preferred for card grids and repeating items where you don't want to manage breakpoints manually.

**Utility classes:**
```
.grid--auto-2  through  .grid--auto-12
.grid--auto-1-2   .grid--auto-2-1   .grid--auto-1-3
.grid--auto-3-1   .grid--auto-2-3   .grid--auto-3-2
```

**Variables (for BEM CSS):**
```css
%root%__grid {
  display: grid;
  grid-template-columns: var(--grid-auto-3);
  gap: var(--grid-gap);
}
```

Override minimum stacking width per instance:
```css
%root%__grid {
  --min: 280px;
}
```

---

### VARIABLE GRID — child-width-driven, automatically responsive

Use when you want the grid to respond to child element width rather than device breakpoints. Ideal for cards where you need to protect a minimum card width. No column count limitations.

```html
<div class="bt-cards__grid variable-grid">
```

```css
/* Set --min at scoped class level — NOT on .variable-grid itself */
%root%__grid {
  --min: 320px;
}
```

Override at breakpoints if needed:
```css
@media (max-width: 768px) {
  %root%__grid {
    --min: 100%;
  }
}
```

No variables — utility class + `--min` only.

---

### AUTO ALTERNATING GRID — repeating text/image row reversal

Use for sections with multiple text + media rows that alternate column order automatically.

1. Wrap all rows in a parent container
2. Apply grid class to each row: `.grid--2` or `.grid--2-3` etc.
3. Apply alternating class to the wrapper at the desired **min-width** breakpoint:

```
.grid-alternate--xl   (alternates at min-width: 1367px and up)
.grid-alternate--l    (alternates at min-width: 993px and up)
.grid-alternate--m    (alternates at min-width: 769px and up)
.grid-alternate--s    (alternates at min-width: 481px and up)
```

Note: These are **min-width** queries — alternation applies at the chosen breakpoint and UP. Typically `.grid-alternate--l` is correct so mobile stays stacked without alternation.

---

### Gap is handled automatically when using grid utility classes (if Auto Contextual Spacing is on).

### When to write custom layout CSS:
- Complex layouts that don't fit any of the above systems
- Specific column spans or named grid areas
- When overriding `--min` for a specific Variable Grid instance
- Any layout where the above tools would require more fighting than writing

---

## 11. SPACING VARIABLES — USE THESE IN CUSTOM CLASSES

Spacing is just spacing — variables have no directional context, use them for padding, margin, gap equally.

```
var(--space-xs)    var(--space-s)    var(--space-m)
var(--space-l)     var(--space-xl)   var(--space-xxl)
```

Fine-tuning with calc (stay within ±1.25x of chosen size):
```css
padding: calc(var(--space-l) / 1.1);   /* slightly less than L */
```

---

## 12. WHAT TO WRITE CSS FOR

Only write CSS when ACSS does NOT cover it, or when you are deliberately overriding a global default.

**Write CSS for:**
- Layout on `brxe-div`: `display: grid`, `display: flex`, `grid-template-columns`, `flex-direction`, `flex-wrap`
- Layout overrides on `brxe-block` / `brxe-container`: `flex-direction: row`, `align-items`, `flex-wrap`, `gap` — never `display: flex` or `flex-direction: column` (Bricks owns those)
- Positioning: `position`, `top/right/bottom/left`, `z-index`
- Dimensional constraints: `max-width`, `min-height`, `aspect-ratio`
- Gap when deviating from global: `gap: calc(var(--grid-gap) * 2)`
- Component-specific spacing where the global default is intentionally wrong
- Color when deviating from body default: `color: var(--text-dark-muted)`
- Anything that is a deliberate exception — always `/* FLAG: reason */`

**Do NOT write CSS for (ACSS or Bricks handles it):**
- Section padding (block and inline) — ACSS
- Body/heading font-family, color, line-height, font-weight — ACSS
- Button padding, colors, radius, transitions, hover states — ACSS
- Card padding, gap, radius, shadow, colors — ACSS
- Icon color, size, padding — ACSS
- Form element base styling — ACSS
- Global border radius — ACSS
- Content/grid/container gap (if Auto Contextual Spacing is on) — ACSS
- Auto-responsive text/heading sizing — ACSS
- `display: flex` on `block` or `container` elements — Bricks
- `flex-direction: column` on `block` or `container` elements — Bricks

---

## 13. THE PRO MODE APPROACH (recommended workflow)

ACSS recommends:
1. Build components with **custom BEM classes** (`.bt-hero`, `.bt-service-card`)
2. Use **ACSS variables** inside those classes (`var(--space-l)`, `var(--primary)`)
3. Use **utility classes** only for one-off contextual changes (`.bg--dark`, `.section--xl`)
4. Never use utility classes on re-usable components (they can't be globally controlled)

This is the "variables are king" philosophy. The result is maximum maintainability — if a token value changes in ACSS dashboard, every component using it updates instantly.

---

*Version 1.2 — Updated March 2026*
*Section 10 rewritten: complete flex vs grid decision guide added, all ACSS layout systems documented from live docs.*
*v1.2: Section 10 core rule updated — Bricks `@layer bricks` defaults for `brxe-block` and `brxe-container` documented. Never write `display:flex` or `flex-direction:column` on block/container BEM classes. Flexbox example updated. Section 12 do-not-write list updated accordingly.*
*Sources: https://docs.automaticcss.com/3.0/flexbox | https://academy.bricksbuilder.io/article/block-element/ | https://www.youtube.com/watch/3elGSZSWTbM*
