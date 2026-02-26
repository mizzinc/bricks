# ACSS-AUTO.md — What ACSS Owns Automatically
## Reference for code generation — do NOT re-declare what ACSS already handles
Version: 1.0 | Based on ACSS 3.0 documentation

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

## 10. GRIDS — ACSS HAS BUILT-IN SYSTEMS

### Auto Grid utility classes (automatically responsive, no breakpoint decisions):
```
.grid--auto-2  through  .grid--auto-12
.grid--auto-1-2   .grid--auto-2-1   .grid--auto-1-3   .grid--auto-3-1
.grid--auto-2-3   .grid--auto-3-2
```

### Standard grid classes:
```
.grid-2  .grid-3  .grid-4  etc.
```

### Auto Grid variables (for custom BEM classes):
```css
.my-grid {
  display: grid;
  grid-template-columns: var(--grid-auto-4);
}
```

### Gap is handled automatically when using grid utility classes (if Auto Contextual Spacing is on).

### When to write custom grid CSS:
- Complex layouts that don't fit the auto-grid pattern
- Specific column spans or named grid areas
- When you need to use `--min` for content-specific stacking:
```css
%root% {
  --min: 280px;
}
```

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
- Layout: `display: grid`, `grid-template-columns`, `flex-direction`, `flex-wrap`
- Positioning: `position`, `top/right/bottom/left`, `z-index`
- Dimensional constraints: `max-width`, `min-height`, `aspect-ratio`
- Gap when deviating from global: `gap: calc(var(--grid-gap) * 2)`
- Component-specific spacing where the global default is intentionally wrong
- Color when deviating from body default: `color: var(--text-dark-muted)`
- Anything that is a deliberate exception — always `/* FLAG: reason */`

**Do NOT write CSS for (ACSS handles it):**
- Section padding (block and inline)
- Body/heading font-family, color, line-height, font-weight
- Button padding, colors, radius, transitions, hover states
- Card padding, gap, radius, shadow, colors
- Icon color, size, padding
- Form element base styling
- Global border radius
- Content/grid/container gap (if Auto Contextual Spacing is on)
- Auto-responsive text/heading sizing

---

## 13. THE PRO MODE APPROACH (recommended workflow)

ACSS recommends:
1. Build components with **custom BEM classes** (`.bt-hero`, `.bt-service-card`)
2. Use **ACSS variables** inside those classes (`var(--space-l)`, `var(--primary)`)
3. Use **utility classes** only for one-off contextual changes (`.bg--dark`, `.section--xl`)
4. Never use utility classes on re-usable components (they can't be globally controlled)

This is the "variables are king" philosophy. The result is maximum maintainability — if a token value changes in ACSS dashboard, every component using it updates instantly.

---

*Last updated: Session Feb 2026 | Based on ACSS 3.0 docs*
