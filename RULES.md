# RULES.md
# Claude Project: Bricks Builder — Code Generation Rules
# Author: Michael van Dinther / Mizzinc
# Version: 2.0
# Last updated: February 2026

---

## 1. PROJECT CONTEXT

You are assisting a senior WordPress developer build websites inside **Bricks Builder**, using **ACSS 3.x** as the design token and utility class system. Your job is to take visual inputs (screenshots, design references, written descriptions) and output clean, structured code that maps directly to Bricks' element model.

All code must be:
- Ready to paste into Bricks or output as valid Bricks JSON
- Written as if a senior developer will read and maintain it
- Consistent with every convention in this file — every time, without exception

**Default output mode: Bricks-ready.** Trust ACSS automatic behaviours. Do not write CSS for things ACSS handles automatically. See `ACSS-AUTO.md` for what ACSS owns.

**Project context is set at session start via the `:root` paste.** Token values, active colour palette, and button colour are all confirmed from there. Never assume which palette is active — read it from the `:root` or flag it.

---

## 1a. PRIMARY WORKFLOW — DESIGN TO CODE

**The primary input is a visual design** — screenshot, Figma export, or reference image. Convert it directly to Bricks-ready HTML/CSS or JSON. Apply all conventions in this file automatically. Do not produce generic code and ask for corrections afterwards.

**Never stall on ambiguity.** Always produce complete, paste-ready output. If something in the design is ambiguous or has no clean ACSS equivalent, produce the best interpretation using whatever CSS gets the job done — a mixture of ACSS and generic CSS, or generic CSS alone — and FLAG it for review.

### CSS resolution hierarchy

When writing CSS for any property, work down this list and stop at the first fit:

**1. ACSS utility class on the element** — if ACSS already expresses it, use it directly
```html
<div class="bt-hero__wrap grid--2 grid--l-1">
```

**2. ACSS variable in BEM CSS** — if you need it in custom CSS and a token covers it
```css
.bt-hero__wrap {
  grid-template-columns: var(--grid-2);
}
```

**3. Raw CSS value** — if no token fits, write the value and FLAG it
```css
.bt-hero__wrap {
  grid-template-columns: 1.1fr 0.9fr; /* FLAG: custom ratio — no ACSS token */
}
```

All three are valid outputs. The output is always complete. The FLAG is the signal to review later — not a reason to withhold code now.

---

## 2. BRICKS ELEMENT → CLASS MAPPING

Every element carries its Bricks element class **first**, followed by BEM class(es).
**26 confirmed element types** from production schemas.

### Layout

| Element | Rendered as | Bricks class | JSON `settings` |
|---|---|---|---|
| `section` | `<section>` | `brxe-section` | `{}` — root has `parent: 0` (integer) |
| `container` | `<div>` | `brxe-container` | `{}` |
| `block` | `<div>` | `brxe-block` | `{}` — flex layout |
| `div` | `<div>` | `brxe-div` | `{}` — grid / generic |

- `block` = flex rows/columns and content groups
- `div` = CSS grid containers and non-flex wrappers
- `container` = max-width wrapper, `margin-inline: auto` — one per section
- Root `section` always has `"parent": 0` (integer zero, not a string)

### Text & Typography

| Element | Rendered as | Bricks class | JSON `settings` |
|---|---|---|---|
| `heading` | `<h1>`–`<h6>` | `brxe-heading` | `{text, tag}` — omitting `tag` defaults to `h2` |
| `text-basic` | `<p>` / inline | `brxe-text-basic` | `{text, tag: "p"}` |
| `text` | `<div>` (rich) | `brxe-text` | `{text: "<p>HTML</p>"}` — full HTML string |
| `text-link` | `<a>` | `brxe-text-link` | `{text}` |

- `text-basic` = single `<p>` or simple text, no inner HTML formatting
- `text` = WYSIWYG / rich content with mixed tags, lists, formatted HTML
- Always set `tag` explicitly on `heading` and `text-basic` — never rely on defaults

### Interactive

| Element | Rendered as | Bricks class | JSON `settings` |
|---|---|---|---|
| `button` | `<a>` / `<button>` | `brxe-button bricks-button` | `{text, style: "btn--[colour]", size: "btn--m"}` |

- `style` maps to the `btn--[colour]` token — value confirmed from `:root` paste at session start
- `size` options: `"btn--xs"`, `"btn--s"`, `"btn--m"`, `"btn--l"`

### Media

| Element | Bricks class | JSON `settings` |
|---|---|---|
| `image` | `brxe-image` | `{}` for static; `{dynamicData: "{featured_image}"}` for dynamic |
| `image-gallery` | `brxe-image-gallery` | `{}` |
| `video` | `brxe-video` | `{videoType, youTubeId, youtubeControls, youtubeDisableFullscreenButton, youtubeHideAnnotationsByDefault, vimeoByline, vimeoTitle, vimeoPortrait, fileControls}` |
| `audio` | `brxe-audio` | `{}` |
| `svg` | `brxe-svg` | `{}` |
| `carousel` | `brxe-carousel` | `{infinite, arrows, prevArrow, nextArrow, prevArrowLeft, nextArrowRight, fields: [{dynamicData, tag, dynamicMargin: {top,right,bottom,left}, id}]}` |

- `video`: `videoType` = `"youtube"` / `"vimeo"` / `"file"` — all settings coexist in the object, `videoType` determines which renders
- `carousel.fields.dynamicMargin` uses **integer px** (not rem): `{top: 20, right: 0, bottom: 20, left: 0}`

### Icons & UI Components

| Element | Bricks class | JSON `settings` |
|---|---|---|
| `icon` | `brxe-icon` | `{icon: {library, icon}}` |
| `icon-box` | `brxe-icon-box` | `{icon: {library, icon}, content: "<h4>…</h4><p>…</p>"}` |
| `divider` | `brxe-divider` | `{}` |
| `breadcrumbs` | `brxe-breadcrumbs` | `{}` |
| `social-icons` | `brxe-social-icons` | `{_padding: {top,right,bottom,left}, _typography: {color: {hex}}, icons: [{label, icon: {library, icon}, background: {hex}}]}` |
| `list` | `brxe-list` | `{}` |

- `icon-box.content` is a raw HTML string — heading and paragraph tags written inline
- `social-icons` colours are set via inline hex in settings (`background.hex`, `_typography.color.hex`) — **not** via ACSS tokens or global classes. This is a Bricks panel setting, not CSS.

### ⚠️ SVG-only rule for icons

**Always use inline SVG — never icon library classes.**

Icon libraries (Themify, Font Awesome, Ionicons) load external font files. This hurts page speed. Use the `svg` element or inline SVG inside a `text-basic`/`div` instead.

```html
<!-- Correct: inline SVG in an svg element or div -->
<div class="brxe-div bt-social__icon" aria-hidden="true">
  <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24">
    <!-- path data -->
  </svg>
</div>

<!-- Wrong: icon library reference -->
<!-- settings: {icon: {library: "themify", icon: "ti-star"}} — DO NOT USE -->
```

When generating JSON that would normally use an `icon` element, use `svg` or `div` with inline SVG content instead. Flag with `/* SVG: source needed */` if the SVG path isn't available.

**Icon libraries are documented here for reference only** — they exist in Bricks and may appear in ingested schemas, but should never be used in new output.

| Library key | Source (reference only) |
|---|---|
| `"themify"` | Themify Icons (`ti-*`) |
| `"fontawesomeBrands"` | Font Awesome Brands (`fab fa-*`) |
| `"ionicons"` | Ionicons (`ion-ios-*`) |

### Dynamic / Post elements (single post & archive templates)

| Element | JSON `settings` |
|---|---|
| `post-title` | `{tag: "h1"}` — always set tag explicitly |
| `post-excerpt` | `{}` |
| `post-content` | `{}` |
| `post-meta` | `{meta: [{dynamicData: "{field}", id: "xxxxxx"}]}` |
| `post-navigation` | `{label: true, title: true, prevArrow: {library, icon}, nextArrow: {library, icon}, image: true}` |
| `post-reading-time` | `{prefix: "Reading time: ", suffix: " minutes"}` |
| `post-reading-progress-bar` | `{}` |
| `post-toc` | `{}` |

**Dynamic data field tokens:**
```
{author_name}          Author display name
{post_date}            Published date
{post_comments}        Comment count
{post_title:link}      Post title as a hyperlink (for carousels/loops)
{post_excerpt:20}      Post excerpt, truncated to 20 words
{featured_image}       Featured image (on image element)
```

### HTML example (confirmed from production)

```html
<section class="brxe-section bt-hero" aria-labelledby="bt-hero-heading">
  <div class="brxe-container bt-hero__wrap">
    <div class="brxe-block bt-hero__content bt-fade-in">
      <h1 class="brxe-heading bt-hero__heading" id="bt-hero-heading">Heading</h1>
      <p class="brxe-text-basic bt-hero__accent-heading">Est. 2006 — New Zealand</p>
    </div>
    <div class="brxe-block bt-hero__content bt-fade-in">
      <p class="brxe-text-basic bt-hero__lead">Lead copy.</p>
      <div class="brxe-div bt-hero__meta bt-fade-in">
        <p class="brxe-text-basic bt-hero__meta-name"><strong>Michael van Dinther</strong></p>
        <p class="brxe-text-basic bt-hero__meta-position">WordPress Developer</p>
      </div>
    </div>
  </div>
</section>
```

---

## 3. BRICKS JSON SCHEMA

When outputting Bricks JSON (copy/paste schema), follow this exact structure. This is confirmed from production usage.

### JSON structure
```json
{
  "content": [ ...elements ],
  "source": "bricksCopiedElements",
  "sourceUrl": "https://[site].com",
  "version": "2.2",
  "globalClasses": [ ...classes ]
}
```

### Element object structure
```json
{
  "id": "xxxxxx",          // 6-char random alphanumeric
  "name": "section",       // Bricks element type
  "parent": "xxxxxx",      // parent element id
  "children": ["xxxxxx"],  // array of child ids
  "settings": {
    "_cssGlobalClasses": ["xxxxxx"],   // array of global class IDs
    "_cssId": "bt-[name]-heading",     // optional — only for aria targets
    "_attributes": [                   // optional — for aria etc.
      {"id": "xxxxxx", "name": "aria-labelledby", "value": "bt-[name]-heading"}
    ],
    "tag": "h1",           // only for heading/text-basic elements
    "text": "Content here" // only for heading/text-basic elements
  },
  "label": "Hero"          // optional human label shown in Bricks panel
}
```

### Global class object structure
```json
{
  "id": "xxxxxx",           // matches the ID referenced in _cssGlobalClasses
  "name": "bt-hero",        // the actual CSS class name
  "settings": {
    "_cssCustom": ".bt-hero {\n  /* all CSS */\n}"
  }
}
```

### Critical JSON rules

**1. All CSS lives in one global class — the root block class.**
The section-level global class (e.g. `bt-hero`) contains ALL component CSS:
`.bt-hero`, `.bt-hero__wrap`, `.bt-hero__content`, `.bt-hero__heading`, etc.
Child element global classes have EMPTY settings (`"settings": {}` or `"settings": []`).

**2. Global class IDs are internal identifiers, not class names.**
An element's `_cssGlobalClasses: ["zawhlo"]` means: apply the global class whose `id` is `"zawhlo"`. That class's `name` field is the actual CSS class applied to the element.

**3. Multiple global classes per element are valid.**
```json
"_cssGlobalClasses": ["zwkehy", "whlsuv"]
// zwkehy → bt-hero__content
// whlsuv → bt-fade-in  (shared utility class, no CSS, just a JS hook)
```

**4. Shared utility classes live as separate global class objects.**
`bt-fade-in` (animation trigger) is its own global class with `"settings": []` — no CSS. Applied to multiple elements via their `_cssGlobalClasses` array.

**5. `_cssId` is only for accessibility targets.**
Only heading elements that are referenced by `aria-labelledby` get a `_cssId`. Never use `_cssId` for styling.

**6. `aria-labelledby` is set on the section via `_attributes`.**
The section element's `_attributes` array contains `{"name": "aria-labelledby", "value": "bt-[name]-heading"}`. The referenced heading has `_cssId: "bt-[name]-heading"`.

### JSON example (bt-hero)
See production hero schema in session history for the complete reference.

---

## 4. BEM NAMING CONVENTION

All custom classes use **BEM methodology** with the `bt-` prefix.
`bt-` = Bricks Theme — namespaces all custom classes away from Bricks core and plugins.

### Structure
```
bt-[block]__[element]--[modifier]
```

### Rules
- Block = component name (`bt-hero`, `bt-services`, `bt-card`)
- Element = semantic part of that block (`__heading`, `__content`, `__meta`)
- Modifier = variant or state (`--dark`, `--featured`, `--active`)
- **No positional names** (`__left`, `__right`, `__top`, `__bottom`)
- **No layout names** — `__inner` → `__wrap`
- `bt-fade-in` = shared utility class (no BEM — it's a global hook, not component-scoped)

---

## 5. SEMANTIC NAMING REFERENCE

### Layout / structural

| Name | Use when |
|---|---|
| `__wrap` | Max-width inner wrapper inside a section |
| `__grid` | CSS grid container |
| `__col` | Order-agnostic column (contact, split CTA) |
| `__content` | Primary text column |
| `__media` | Image, video, SVG, illustration column |
| `__item` | Repeating child unit inside a grid or list |

### Typography

| Name | Use when |
|---|---|
| `__heading` | Primary heading (h1–h3) within a block |
| `__accent-heading` | Overline / eyebrow / kicker above main heading |
| `__subheading` | Secondary heading below main heading |
| `__lead` | Intro / lede paragraph — first prominent `<p>` |
| `__body` | Standard body copy paragraph |
| `__caption` | Small supporting text, credits |
| `__cite` | Attribution on quotes |

### Meta / attribution

| Name | Use when |
|---|---|
| `__meta` | Wrapper for name/role/attribution grouping |
| `__meta-name` | Name of a person (bold, slightly larger) |
| `__meta-position` | Role, title, URL below a name |
| `__meta-date` | Date or timestamp |

### Interactive

| Name | Use when |
|---|---|
| `__cta` | Call-to-action wrapper div |
| `__cta-link` | Text link styled as CTA |
| `__url` | Plain URL display link |
| `__email` | Email address link |

### Modifier strategy for site-wide components

When a component (like `bt-hero`) is used across multiple pages with variations, the approach depends entirely on **what is varying**. There are two tools available — use the right one for the job.

---

#### Decision rule: three tools, in priority order

Before reaching for a BEM modifier, check whether ACSS already handles the variation. There are three tools — use the first one that fits.

---

**Tool 1 — ACSS background utility class**
Use when the variation is a colour scheme change (dark, light, ultra-dark etc.).

`bg--dark`, `bg--ultra-dark`, `bg--light` etc. trigger ACSS Automatic Color Relationships — background, text, headings, links, buttons, and borders all flip automatically. **You write no CSS at all.**

```html
<section class="brxe-section bt-hero bg--dark">
```

`bt-hero--dark` should not exist. `bg--dark` already does the job completely.

**Fine-tuning via localised variable override:**
If you need to adjust a specific colour within a `bg--dark` context, override the token ACSS is already reading — don't write property rules:

```css
/* Scope the override to your component — ACSS reads it automatically */
.bt-hero {
  --bg-dark-heading: var(--white);
}
```

This is cleaner than writing `color:` rules because ACSS owns those properties. Reassign the variable, let ACSS apply it.

---

**Tool 2 — ACSS layout/structural utility class on the inner element**
Use when the variation is a standard grid or layout change.

```html
<!-- Grid lives on __wrap — apply utility class there -->
<div class="brxe-container bt-hero__wrap grid--2 grid--l-1">
```

No modifier needed. No extra global class. No CSS to write. The utility class IS the variation.

For custom ratios not expressible via utility class, write it in BEM CSS:
- Shared across all instances → base `bt-hero__wrap` CSS
- Unique to one instance → written in that page's component CSS, no modifier class

---

**Tool 3 — BEM modifier**
Use only when the variation cannot be expressed by Tools 1 or 2. This means structural differences — reversed column order, a layout constraint, a padding treatment that needs to persist across breakpoints and can't be captured by a utility class alone.

```html
<section class="brxe-section bt-hero bt-hero--centered">
<section class="brxe-section bt-hero bt-hero--tall">
<section class="brxe-section bt-hero bt-hero--reversed">
```

A modifier contains **only the delta** — what changes from the base. If the modifier CSS is larger than the base, the variation is its own block, not a modifier.

```css
/* bt-hero--reversed: flip columns without touching DOM */
.bt-hero--reversed .bt-hero__wrap > *:first-child { order: 2; }
.bt-hero--reversed .bt-hero__wrap > *:last-child  { order: 1; }
```

---

#### What goes where

| Variation type | Tool | How |
|---|---|---|
| Dark / light background + all colour relationships | Tool 1 | `bg--dark` on the section element |
| Fine-tune a colour within a bg context | Tool 1 + variable override | `--bg-dark-heading: var(--white)` in BEM base CSS |
| Standard equal-column grid | Tool 2 | `grid--2 grid--l-1` on `__wrap` |
| Standard fractional grid | Tool 2 | `grid--1-2 grid--l-1` on `__wrap` |
| Custom ratio (e.g. 1.6fr 0.9fr) | BEM base CSS | In `bt-hero__wrap` rule |
| Reversed column order | Tool 3 | `bt-hero--reversed` modifier |
| Single-column centered | Tool 3 | `bt-hero--centered` modifier |
| Padding / spacing override | Tool 2 or 3 | `section--xl` utility, or `bt-hero--tall` modifier |

---

#### Custom grid ratios (e.g. 1.6fr 0.9fr)

ACSS utility classes express equal-column grids and standard fractional splits. For custom ratios, write CSS:

- Shared across all hero instances → in the base `bt-hero__wrap` rule
- Unique to one instance → written directly in that component's CSS, no modifier needed

---

#### Naming modifiers — describe the variation, never the page or implementation

Name by *what it does visually or structurally*, not *where it lives* or *how it's built*. A name tied to a page becomes wrong the moment that variation appears on a second page. A name tied to an implementation becomes wrong the moment the implementation changes.

| Use | Don't use | Why |
|---|---|---|
| `bt-hero--centered` | `bt-hero--about` | Page names rot |
| `bt-hero--tall` | `bt-hero--grid-1` | Implementation detail, not purpose |
| `bt-hero--reversed` | `bt-hero--home` | Another page could use the same treatment |
| `bt-hero--flush` | `bt-hero--contact` | Describes the visual effect — portable |
| `bt-hero--centered` | `bt-hero-alpha` / `bt-hero-1` | Letters and numbers communicate nothing |
| `bg--dark` on the section | `bt-hero--dark` | ACSS already handles dark colour scheme completely |

`bt-hero--grid-2` is a trap: if the grid changes, the name is wrong. If `grid--2` is already an ACSS utility, there's no reason to invent a modifier that duplicates it.

---

#### When a variation is structurally distinct — give it its own block name

If a component is architecturally different enough that you hesitate to call it a modifier, it is its own block entirely. **Don't hyphenate it back to the parent.**

The test: if the component's CSS would be larger than the base component's CSS, it's a new block.

```
bt-hero          ← text-dominant hero, grid via ACSS utilities
bt-hero--tall    ← minor structural delta — extra vertical space
bt-poster        ← full-height image-driven layout — its own block, not a hero variant
```

`bt-poster` not `bt-hero-poster`. Dropping the `hero-` prefix gives it independence. The name describes what it *is*, not what it's derived from. It can appear on any page and the name is never wrong.

**Never use:**
- Page names: `bt-home-hero`, `bt-contact-hero` — breaks when reused elsewhere
- Numbers: `bt-hero-1`, `bt-hero-2` — communicates nothing
- Greek alphabet: `bt-hero-alpha`, `bt-hero-beta` — numbers with a costume
- Implementation details: `bt-hero-vh-100` — lies when the value changes

---

#### In Bricks JSON

Each modifier is its own global class object. Both are applied via `_cssGlobalClasses` on the section:

```json
// Modifier: structural variation
"_cssGlobalClasses": ["zawhlo", "xxxxxx"]
// zawhlo → bt-hero            (base — all shared CSS)
// xxxxxx → bt-hero--centered  (modifier — delta CSS only)

// bg--dark: applied directly as an ACSS utility class (no global class object needed)
// Add "bg--dark" to the element's _cssGlobalClasses if it exists as a global class,
// or apply it via the Bricks style panel as a utility class
```

### Reusable / global utility classes (not block-scoped)

| Class | Use when |
|---|---|
| `bt-fade-in` | Animation trigger — JS hook. No CSS. Apply to any element. |
| `bt-section-kicker` | Overline label above a section. Flex row with rule line. |
| `grid--borders` | Shared borders on flush grid layouts — no doubling. Apply to the grid container. |

---

### `%root%` — Bricks global class self-reference

In Bricks global class CSS, `%root%` refers to the class itself. **Use it for every selector in a global class CSS block** — not just the root rule. This makes the entire block portable and class-agnostic.

```css
/* In a global class named bt-services — all selectors use %root% */
%root%-card { border: 0; }
%root%-card__number { font-size: var(--text-xs); }
%root%-card__heading { text-transform: uppercase; }
%root%__grid { display: grid; }
%root% .bt-section-kicker { position: sticky; }
```

Never hardcode the class name inside its own CSS block. This applies to child BEM selectors, descendant selectors, and modifier selectors equally.

#### Double selector specificity — override framework styles without `!important`

When a single selector loses to ACSS card framework or other framework specificity, double it:

```css
/* May lose to framework */
%root%-card { border: 0; }

/* Wins without !important */
%root%-card%root%-card { border: 0; }
```

Use this whenever a framework style isn't being overridden by a single selector.

---

### `grid--borders` — shared grid borders without doubling

A reusable global class that draws borders between grid cells using pseudo-elements and `overflow: hidden` on the parent. No border doubling on shared edges. Works at any column count and collapses correctly at all breakpoints.

```css
%root% {
  --gap: 0em;
  --card-border-color: var(--light-card-border-color);
  --line-thickness: 1px;
  --line-color: var(--card-border-color);
  --line-offset: calc(var(--gap) / 2);
  overflow: hidden;
}
%root% > * {
  position: relative;
}
%root% > *::before,
%root% > *::after {
  content: "";
  background-color: var(--line-color);
  position: absolute;
}
%root% > *::after {
  inline-size: 100vw;
  block-size: var(--line-thickness);
  inset-block-start: calc(var(--line-offset) * -1);
  inset-inline-start: 0;
}
%root% > *::before {
  inline-size: var(--line-thickness);
  block-size: 100vh;
  inset-block-start: 0;
  inset-inline-start: calc(var(--line-offset) * -1);
}
```

**Usage notes:**
- Apply to the grid container div alongside the BEM grid class
- `--line-color` defaults to `var(--card-border-color)` → `var(--light-card-border-color)` — stays in sync with ACSS card framework border token
- When used with card framework: override the automatic card border with `border: 0` on the card class so the grid handles borders exclusively
- `--line-color` can be overridden per-component: `.bt-services__grid { --line-color: var(--border-color-dark); }`
- Caveat: child elements using both `::before` and `::after` for other purposes will conflict

---

## 6. CSS ARCHITECTURE

### Where CSS lives
ALL component CSS lives in **one global class object** — the root block class.

```css
/* Everything for bt-hero goes in the bt-hero global class CSS */
.bt-hero { border-bottom: 1px solid var(--border-color-dark); }
.bt-hero__wrap { display: grid; ... }
.bt-hero__content { row-gap: var(--paragraph-spacing); }
.bt-hero__heading { text-transform: uppercase; }
/* etc. */
```

Child elements (bt-hero__wrap, bt-hero__content etc.) have **empty CSS settings** in the JSON. Their Bricks global class objects exist only to apply the class name — no CSS.

### Property order (consistent top to bottom)

```css
.bt-example {
  /* 1. Layout / display */
  display: grid;
  grid-template-columns: 1.6fr 0.9fr;
  gap: var(--space-xxl);
  align-items: end;

  /* 2. Position */
  position: relative;
  z-index: 1;

  /* 3. Box model */
  width: 100%;
  max-inline-size: var(--content-width);
  padding-block: var(--section-space-xl);

  /* 4. Visual */
  background: var(--neutral-ultra-light);
  border: var(--border);
  border-radius: var(--radius);

  /* 5. Typography */
  font-size: var(--text-m);
  font-weight: 500;
  color: var(--text-dark);

  /* 6. Transitions */
  transition: var(--transition);
}
```

### Column reordering — use CSS order, never positional markup

```css
/* Flip visual order without changing DOM */
.bt-section__col:first-child { order: 2; }
.bt-section__col:last-child  { order: 1; }
```

### Eyebrow / accent-heading ordering pattern
When an eyebrow needs to appear visually above an h1 but the h1 is first in DOM (for SEO/a11y):

```css
.bt-hero__accent-heading {
  order: -1;  /* displays before h1 even though h1 comes first in DOM */
}
```
The block/content container must be `display: flex; flex-direction: column` for `order` to work.

---

## 7. ACSS VARIABLE USAGE

**Always use `var(--token)` in custom CSS. Never hardcode values.**

### Correct patterns

```css
/* Colours */
color: var(--text-dark-muted);
background: var(--neutral-ultra-light);
border-color: var(--border-color-dark);

/* Spacing */
gap: var(--space-xxl);
padding-block: var(--section-space-xl);
row-gap: var(--paragraph-spacing);

/* Typography */
font-size: var(--text-xs);
letter-spacing: 0.12em;   /* fine-tuning only — no token for this */
font-weight: 500;         /* no token — use raw value */

/* Layout */
grid-template-columns: 1.6fr 0.9fr;  /* custom ratio — no token */
```

### When no ACSS token exists

Create a local custom property scoped to the root block, flagged for review:

```css
/* Custom ratios that don't have ACSS tokens */
.bt-hero__wrap {
  grid-template-columns: 1.6fr 0.9fr; /* FLAG: custom ratio, not tokenised */
}
```

### Project token notes
- Active colour palette and button colour are set by the `:root` paste at session start
- If no `:root` has been provided, flag before writing any colour or button CSS
- Use semantic tokens (`var(--primary)`, `var(--neutral)`) — never raw hex

---

## 8. RESPONSIVE BEHAVIOUR

### Breakpoints (confirmed from automatic.css)

| Name | Value | Usage |
|---|---|---|
| xl | `max-width: 1366px` | Large desktop → tablet landscape |
| l | `max-width: 992px` | Tablet portrait |
| m | `max-width: 768px` | Mobile landscape |
| s | `max-width: 480px` | Mobile portrait |

### ACSS utility class breakpoint syntax
```html
grid--3 grid--l-2 grid--m-1     ← 3→2→1 at breakpoints
section--xl section--m-m         ← xl padding default, m padding at ≤768px
```

### Custom CSS breakpoints
```css
@media (max-width: 992px) { /* tablet */ }
@media (max-width: 768px) { /* mobile */ }
@media (max-width: 480px) { /* small mobile */ }
```

### Responsive rules
- Grid layouts collapse to `grid-template-columns: 1fr` at appropriate breakpoint
- Use ACSS `section--[bp]-[size]` utility classes for section padding overrides
- For component-specific padding, write `@media` in the component CSS
- Use ACSS breakpoints (1366, 992, 768, 480) — avoid custom breakpoints unless the design specifically demands it
- Never hide content on mobile unless explicitly designed that way — flag with comment

### Mobile pattern for 2-column hero
```css
@media (max-width: 768px) {
  .bt-hero__wrap {
    grid-template-columns: 1fr;
    gap: var(--space-l);
  }
}
```

---

## 9. ACCESSIBILITY

- Every `<section>` gets `aria-labelledby="bt-[name]-heading"`
- The referenced heading gets `id="bt-[name]-heading"` (in Bricks: `_cssId`)
- `<img>` elements must have descriptive `alt` — never empty unless purely decorative
- Interactive elements must have discernible text or `aria-label`
- Decorative icons/arrows get `aria-hidden="true"`
- Heading hierarchy must be logical — never skip levels for visual effect; adjust size with CSS
- Colour contrast must meet WCAG AA

### Aria pattern (JSON)
```json
// On the section element:
"_attributes": [{"id": "xxx", "name": "aria-labelledby", "value": "bt-hero-heading"}]

// On the heading element:
"_cssId": "bt-hero-heading"
```

---

## 10. HTML STRUCTURAL RULES

- One `<h1>` per page — always in the hero section
- `<section>` for distinct thematic page areas
- `<article>` for self-contained repeating content (blog posts, case studies)
- `<nav>` for navigation
- `<footer>` for site footer — not a `<section>`
- `<blockquote>` for pull quotes / testimonials
- `<cite>` for attribution on quotes
- Avoid unnecessary wrappers — if an element has one child, question the wrapper

---

## 11. SESSION START PROTOCOL

Before writing any code, confirm:

1. **`:root` block** — paste live ACSS `:root` from DevTools or ACSS export.
   This overrides everything in `ACSS-TOKENS.md`. Until provided, use `ACSS-TOKENS.md` as fallback.

2. **Typeface** — confirm heading font and body font before any typography rules.
   If unconfirmed, use `/* FLAG: confirm typeface */`.

3. **Output mode** — Bricks-ready (default) or Standalone preview (explicit request).
   See `OUTPUT-MODES.md`.

4. **Component brief** — screenshot, description, or reference URL.

### Session start template
```
Starting build: [Client Name]

:root {
  /* paste DevTools :root here */
}

Typeface: [heading font / body font]
Mode: Bricks-ready

First component: [description or screenshot]
```

---

## 12. OUTPUT FORMAT

### Default: Bricks-ready HTML + CSS

Output in this order:
1. Class map comment block (structure overview)
2. HTML with Bricks + BEM classes
3. Component CSS (all in one block, as it would live in the root global class)

### Class map comment block
```html
<!-- ============================================================
  COMPONENT: bt-[name]
  brxe-section      → bt-[name]               (holds all CSS)
  brxe-container    → bt-[name]__wrap
  brxe-block        → bt-[name]__content      + bt-fade-in
  brxe-heading      → bt-[name]__heading      (h1, id for aria)
  brxe-text-basic   → bt-[name]__accent-heading
  brxe-text-basic   → bt-[name]__lead
  brxe-div          → bt-[name]__meta         + bt-fade-in
  brxe-text-basic   → bt-[name]__meta-name
  brxe-text-basic   → bt-[name]__meta-position
============================================================ -->
```

### CSS block header
```css
/* ── bt-[name] ────────────────────────────────────────────
   Global class: bt-[name]
   All component styles live here.
   ────────────────────────────────────────────────────── */
```

### When JSON output is requested
Output valid Bricks copy/paste JSON following the schema in Section 3.
State at the top:
```
// OUTPUT: Bricks Builder JSON
// Import via: paste into Bricks canvas (copy/paste)
```

---

## 12a. CSS PATTERNS REFERENCE

### Token localisation — reassign the variable, never the property

When a component needs a value different from the ACSS global default, reassign the token the framework is already reading. The framework then applies it automatically.

```css
/* Heading weight — reassign token, not property */
%root%-card__heading {
  --heading-font-weight: 500;
  font-weight: var(--heading-font-weight);
}

/* bg--dark context — reassign what ACSS reads */
%root% {
  --bg-dark-heading: var(--white);
}
```

### Sticky positioning — overflow gotcha

`position: sticky` silently fails inside any ancestor with `overflow: hidden`, `overflow: auto`, or `overflow: scroll`. Always unset overflow before applying sticky:

```css
@media (max-width: 992px) {
  /* grid--borders sets overflow: hidden — must unset for sticky to work */
  %root% .grid--borders {
    overflow: unset;
  }

  %root%-card {
    position: sticky;
    inset-block-start: var(--sticky-offset, 0);
  }
}
```

### Sticky offset — build from tokens, not magic numbers

Always construct sticky offsets from ACSS tokens. The only acceptable raw value is a fine-tuning remainder that has no token equivalent — FLAG it:

```css
%root%-card {
  --sticky-offset: calc(
    var(--header-height) +
    var(--section-padding-block) +
    var(--space-xl) +
    23px /* FLAG: 23px = fine-tuning remainder, review if spacing tokens change */
  );
}

/* Each subsequent card adds its own height to the offset */
%root%-card:nth-child(2) {
  --sticky-offset: calc(
    var(--header-height) +
    var(--section-padding-block) +
    var(--space-xl) +
    23px +
    (var(--card-padding) * 1.5) +
    var(--content-gap)
  );
}
```

### Sticky kicker — pin section label during scroll interactions

When cards stack on scroll, the section kicker should also stick so context is maintained:

```css
%root% .bt-section-kicker {
  --sticky-offset: calc(var(--header-height) + var(--section-padding-block));
  position: sticky;
  inset-block-start: var(--sticky-offset, 0);
}
```

---

## 13. FLAGS AND REVIEW COMMENTS

Add `/* FLAG: */` when:
- A design decision is ambiguous from the screenshot
- A value has no ACSS token equivalent
- A layout requires a workaround
- An animation needs JS not yet specified
- Heading hierarchy would break for visual reasons

```css
/* FLAG: custom grid ratio — not tokenised */
grid-template-columns: 1.6fr 0.9fr;

/* FLAG: confirm font-weight token exists or use raw value */
font-weight: 500;
```

---

## 14. WHAT NOT TO DO

- Do not assume which colour palette is active — confirm from the `:root` paste or flag it
- Do not write CSS for things ACSS handles automatically (section padding, button styling, card base, typography defaults). See `ACSS-AUTO.md`
- When using card classes (`bt-[name]-card`), always output this FLAG comment in the JSON or CSS:
  `/* FLAG: add .bt-[name]-card to Manual Card Selectors in ACSS → Cards → Targeting Settings */`
  Never silently assume auto card styling will fire. On established projects the exclusion list is often heavily modified. Manual selectors are the safe, explicit opt-in for new components.
- Do not use ACSS utility classes for colours not confirmed active
- Do not use utility classes on reusable components — use `var()` in BEM classes instead
- Do not use inline styles
- Do not use `id` attributes for styling — only for `aria-labelledby` targets
- Do not generate placeholder copy — use `[HEADING]`, `[BODY COPY]`, `[CLIENT NAME]`
- Do not assume a font is available — reference via ACSS variables or flag
- Do not use positional class names (`__left`, `__right`, `__top`, `__bottom`)
- Do not use custom breakpoints unless the design explicitly requires it — align to ACSS breakpoints (1366, 992, 768, 480)
- Do not create separate CSS files for child elements — all CSS goes in the root block's global class
- Do not hardcode class names inside a global class CSS block — always use `%root%` for every selector
- Do not use `!important` to override framework specificity — use double selector instead: `%root%-card%root%-card`
- Do not apply `position: sticky` inside a container with `overflow: hidden` — sticky will silently break. Always `overflow: unset` the container first.

---

## 15. REFERENCE FILES

| File | Purpose |
|---|---|
| `ACSS-TOKENS.md` v4.0 | Project-agnostic token catalogue — values from `:root` paste |
| `ACSS-AUTO.md` v1.0 | What ACSS handles automatically — do not duplicate |
| `ACSS-CHEATSHEET.md` v1.0 | All utility classes + breakpoint modifier syntax |
| `OUTPUT-MODES.md` v1.0 | Bricks-ready vs Standalone preview modes |

---

*End of RULES.md — version 2.2*
*Update: Added primary workflow (Section 1a) — design-to-code, never stall on ambiguity.*
*Added CSS resolution hierarchy: utility class → ACSS variable → raw value + FLAG.*
