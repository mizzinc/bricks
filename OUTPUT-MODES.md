# OUTPUT-MODES.md — Code Generation Modes
## How Claude generates code in a Bricks Builder + ACSS environment
Version: 1.0

---

## THE CORE PROBLEM

ACSS has two layers:

1. **Variables** — defined in `:root`, available everywhere, fully portable
2. **Automatic behaviours** — CSS rules injected by ACSS into the stylesheet at runtime

The `:root` paste at session start gives Claude all the token values. But the automatic behaviours (section padding, auto card styling, button classes, etc.) only fire when ACSS is actually installed and running in WordPress/Bricks.

This means code generated outside of a live ACSS environment will look under-spaced and under-styled in a plain browser preview — even though the code is correct for production.

---

## MODE 1 — BRICKS-READY (default)

**Use for:** All production code intended to be imported into Bricks Builder.

**Approach:**
- Write lean, clean code trusting ACSS to handle its automatic responsibilities
- Sections get NO padding CSS — ACSS applies M section padding automatically
- Buttons get the class only (`.btn--primary`) — no padding, color, or hover CSS
- Cards get BEM classes only — ACSS Card Framework handles padding, radius, shadow
- Icons get `data-icon` attribute only — ACSS Icon Framework handles everything
- Gap is omitted where Auto Contextual Spacing will apply it
- Typography properties (color, line-height, font-weight) omitted where ACSS global defaults apply

**Output characteristics:**
- Minimal CSS — only layout, positioning, dimensional constraints, and deliberate overrides
- All spacing via `var(--space-*)` and `var(--section-space-*)`
- All colours via ACSS semantic tokens
- Will look bare/unstyled in a plain browser but correct in Bricks + ACSS

**This is always the default unless otherwise requested.**

---

## MODE 2 — STANDALONE PREVIEW

**Use for:** Browser previews, client presentations, design review outside of Bricks.

**Approach:**
- Simulate ACSS automatic behaviours with explicit CSS
- Add an `/* ACSS SIMULATION — remove before importing to Bricks */` block that approximates:
  - Section block padding
  - Content and grid gap
  - Button base styles
  - Card base styles
  - Body/heading typography defaults
  - Any other relevant automatic behaviours

**Output characteristics:**
- Heavier CSS with simulation scaffolding clearly flagged
- Visually accurate representation of the finished component
- NOT production-ready — simulation block must be removed before Bricks import
- Production CSS block is still clean and separated from simulation block

**Request this mode explicitly:** "Give me a standalone preview" or "include ACSS simulation."

---

## SWITCHING BETWEEN MODES

You can request either mode at any time:

- "Bricks-ready" or "production output" → Mode 1
- "Standalone preview" or "add ACSS simulation" → Mode 2
- "Show me both" → Mode 1 code block + Mode 2 simulation block, clearly separated

---

## SIMULATION BLOCK FORMAT

When Mode 2 is requested, the simulation CSS is always isolated and clearly flagged:

```css
/* ============================================
   ACSS SIMULATION BLOCK
   Approximates ACSS automatic behaviours for
   standalone browser preview only.
   REMOVE THIS BLOCK before importing to Bricks.
   ============================================ */

section {
  padding-block: clamp(3rem, 6vw, 6rem);
  padding-inline: clamp(1.25rem, 4vw, 3.75rem);
}

/* ... other simulated behaviours ... */

/* END ACSS SIMULATION BLOCK */
```

---

## WHAT THE :root PASTE SOLVES

At session start, pasting the live `:root {}` block from DevTools gives Claude:

- All confirmed colour values for every token
- All confirmed spacing scale values
- All confirmed typography size values
- All confirmed radius, border, transition tokens

This means Mode 1 output uses real values — not inferred or guessed — and will match the live site exactly when imported into Bricks.

**Without a `:root` paste, Claude falls back to values from ACSS-TOKENS.md, which may contain inferred values for unconfirmed tokens.**

---

## SESSION START REMINDER

Always paste at the start of a build session:

```
Starting build: [Client Name]

:root {
  /* paste full DevTools :root output here */
}

Typeface: [heading font / body font]
Mode: Bricks-ready [or: Standalone preview]

First component: [description or screenshot]
```

---

*Last updated: Session Feb 2026*
