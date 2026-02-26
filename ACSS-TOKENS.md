# ACSS-TOKENS.md — CSS Variable Catalogue
## All available ACSS tokens — values supplied by :root paste at session start
Version: 4.0

> **How to use:** This file lists every available ACSS token by category.
> Values are NOT stored here — they come from the `:root` paste at session start.
> Always use `var(--token)` in custom CSS. Never hardcode values.
> If no `:root` has been provided, flag any colour, spacing, or typography value before writing it.

---

## HOW VALUES ARE SUPPLIED

At the start of every build session, paste the live ACSS `:root` from DevTools:

```
Starting build: [Client Name]

:root {
  /* paste full DevTools :root output here */
}

Typeface: [heading font / body font]
Active button colour: [btn--primary / btn--neutral / btn--base]
```

This is the single source of truth for all token values on that project.

---

## 1. COLOUR TOKENS

### Brand palette
Each colour has a full shade scale. Replace `[colour]` with `primary`, `secondary`, `base`, or `neutral`.

```
--[colour]
--[colour]-hover
--[colour]-ultra-light
--[colour]-light
--[colour]-semi-light
--[colour]-semi-dark
--[colour]-dark
--[colour]-ultra-dark
```

HSL component access (for `color-mix()` and custom tinting):
```
--[colour]-h   --[colour]-s   --[colour]-l
```

Transparency scale (10–90 in steps of 10):
```
--[colour]-trans-10  →  --[colour]-trans-90
--[colour]-light-trans-10  →  --[colour]-light-trans-90
--[colour]-dark-trans-10   →  --[colour]-dark-trans-90
--[colour]-ultra-dark-trans-10  →  --[colour]-ultra-dark-trans-90
```

### Absolute colours
```
--white         --black
--white-trans-10 → --white-trans-90
--black-trans-10 → --black-trans-90
```

### Status colours
```
--danger   --danger-hover   --danger-light   --danger-dark   --danger-ultra-light   --danger-ultra-dark
--warning  --warning-hover  --warning-light  --warning-dark  --warning-ultra-light  --warning-ultra-dark
--info     --info-hover     --info-light     --info-dark     --info-ultra-light     --info-ultra-dark
--success  --success-hover  --success-light  --success-dark  --success-ultra-light  --success-ultra-dark
```

Each status colour also has transparency variants:
```
--danger-trans-10  →  --danger-trans-90  (and light/dark variants)
```

### Semantic background / text tokens
```
--bg-ultra-light    --bg-light    --bg-dark    --bg-ultra-dark
--text-light        --text-light-muted
--text-dark         --text-dark-muted
--body-bg-color     --body-color
--heading-color
```

---

## 2. TYPOGRAPHY TOKENS

### Heading sizes
```
--h1   --h2   --h3   --h4   --h5   --h6
```

### Text sizes
```
--text-xs   --text-s   --text-m   --text-l   --text-xl   --text-xxl
```

### Typography globals (ACSS applies these automatically — reference only, do not re-declare)
```
--heading-font-family      --text-font-family
--heading-font-weight      --text-font-weight
--heading-line-height      --text-line-height
--heading-letter-spacing   --text-letter-spacing
--heading-text-wrap        --text-text-wrap
--paragraph-spacing
```

---

## 3. SPACING TOKENS

### Component-level spacing
```
--space-xs   --space-s   --space-m   --space-l   --space-xl   --space-xxl
```

### Section-level spacing
```
--section-space-xs   --section-space-s   --section-space-m
--section-space-l    --section-space-xl  --section-space-xxl
```

### Contextual gap tokens
```
--grid-gap         (gap between grid items)
--content-gap      (gap between content elements within a container)
--container-gap    (gap between containers within a section)
```

### Layout tokens
```
--content-width        (max site width)
--content-width-safe   (with safe inline padding: min(content-width, 100% - 2*gutter))
--gutter               (inline padding from viewport edge)
--section-padding-block
--section-padding-x
```

---

## 4. GRID TOKENS

### Fixed grid columns (for custom BEM classes)
```
--grid-1  through  --grid-12       (repeat(N, minmax(0, 1fr)))
```

### Fractional splits
```
--grid-1-2   --grid-2-1   --grid-1-3   --grid-3-1   --grid-2-3   --grid-3-2
```

### Auto-responsive (preferred — collapses automatically)
```
--grid-auto-2  through  --grid-auto-12
--grid-auto-1-2   --grid-auto-2-1   --grid-auto-1-3
--grid-auto-3-1   --grid-auto-2-3   --grid-auto-3-2
```

### Width tokens (percentage of content-width)
```
--width-xs   --width-s   --width-m   --width-l   --width-xl   --width-xxl   --width-content
--width-10   --width-20  --width-30  --width-40  --width-50
--width-60   --width-70  --width-80  --width-90
```

---

## 5. BORDER & RADIUS TOKENS

### Borders
```
--border              (full shorthand: width + style + color)
--border-width        --border-style
--border-color-dark   --border-color-light
--divider-color-dark  --divider-color-light
```

### Radius scale
```
--radius              (global default — use this everywhere)
--radius-xs   --radius-s   --radius-m   --radius-l   --radius-xl   --radius-xxl
--radius-50   --radius-circle
```

---

## 6. BUTTON TOKENS

ACSS owns all button styling automatically when a `btn--[colour]` class is present.
These tokens are available for fine-tuning individual instances only.

```
--btn-padding-block      --btn-padding-inline
--btn-min-width          --btn-line-height
--btn-font-size          --btn-font-weight
--btn-border-width       --btn-border-radius
--btn-background         --btn-color
--btn-border-color
```

**Active button colour** is confirmed from the `:root` paste — do not assume `btn--primary`.

---

## 7. CARD TOKENS

ACSS owns all card styling automatically for any class containing `card`.
These tokens are available for project-level overrides only.

```
--card-padding      --card-gap       --card-radius
--card-heading-size --card-text-size
--light-card-background    --dark-card-background
```

---

## 8. ICON TOKENS

```
--icon-size        (default)
--icon-size-xs   --icon-size-s   --icon-size-m
--icon-size-l    --icon-size-xl  --icon-size-xxl
--icon-padding   --icon-radius
```

> **Reminder:** Always use inline SVG, not icon library classes. See RULES.md Section 2.

---

## 9. TRANSITION & SHADOW TOKENS

```
--transition               (full shorthand)
--transition-duration      --transition-timing
```

```
--box-shadow-m   --box-shadow-l   --box-shadow-xl
```

---

## 10. FOCUS TOKENS

```
--focus-color   --focus-width   --focus-offset
```

---

## 11. COLOUR SCHEME SYSTEM

ACSS supports two colour schemes applied as classes on any element.

```
.color-scheme--main    (default — light bg, dark text)
.color-scheme--alt     (inverted — all neutral tokens flip)
```

When `.color-scheme--alt` is active, neutral scale inverts:
`--neutral` becomes the lightest value, `--neutral-ultra-light` becomes dark.
All components inside the element automatically inherit the flipped tokens.

---

## TOKEN USAGE RULES

1. **Always `var(--token)` in custom CSS** — never raw hex, px, or rem values
2. **Values come from the `:root` paste** — this file is the catalogue, not the source of truth
3. **When no `:root` is available**, add `/* FLAG: confirm value from :root */` and use a reasonable estimate based on ACSS 3.x defaults
4. **Fine-tuning with calc** — stay within ±1.25x of the chosen scale step:
   ```css
   padding: calc(var(--space-l) / 1.1);   /* slightly under L */
   gap: calc(var(--grid-gap) * 1.5);       /* between M and XL */
   ```
5. **Semantic over specific** — prefer `var(--text-dark-muted)` over `var(--neutral-semi-dark)` when the intent is muted text

---

*Version 4.0 — Rebuilt as project-agnostic token catalogue*
*Values supplied per-project via :root paste at session start*
