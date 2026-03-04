# BRX-ANIMATE.md — Bricks Native Interaction Animation Utilities
## Global animation helper classes for Bricks Builder interactions
Version: 1.1 | Production-confirmed | Works alongside native Bricks interaction system

---

## OVERVIEW

This system provides two tiers of animation control for Bricks Builder native interactions.
Tier 1 requires no extra classes. Tier 2 opts in for stagger and CSS variable timing control.

**Live in:** `global.css`
**Dependencies:** Bricks native interactions (Enter viewport → Start animation)
**No JavaScript required** — pure CSS cascade.

---

## SCOPE — BRX-ANIMATE VS GSAP

This system covers **Bricks native interactions only** — the built-in Enter viewport →
Start animation panel in Bricks Builder.

GSAP is a separate animation system and may be used:
- **Alongside** `.brx-animate` (e.g. GSAP handles hero timelines, `.brx-animate` handles
  scroll-triggered section elements)
- **Instead of** `.brx-animate` (e.g. a component is fully GSAP-driven with no Bricks
  interactions at all)

| System | Trigger mechanism | Timing control | Use case |
|---|---|---|---|
| `.brx-animate` | Bricks interaction panel | CSS variables | Simple scroll-triggered fade/slide-ins, stagger groups |
| GSAP | JavaScript (ScrollTrigger, timelines) | JS config | Complex sequences, scrub animations, per-element choreography, anything requiring a timeline |

**Never mix both systems on the same element.** Pick one per element. Mixing will produce
conflicting `animation-duration` and `animation-delay` values.

> GSAP patterns are documented separately. See `GSAP.md` when available.

---

## ANIMATION APPROACH — TWO TIERS

### Tier 1 — Global keyframe override (zero-class, default)

The `fadeInUp` keyframe is overridden globally in `global.css`. Any element with a
Bricks interaction set to `fadeInUp` animates correctly without any extra class.

**Use this for:** any single element that just needs a clean scroll-triggered fade-in.
```
Element
  └─ No extra class needed
  └─ Interaction: Enter viewport → Start animation → fadeInUp
  └─ Duration:    Set in panel
  └─ Delay:       Set in panel
  └─ Run only once: ON
```

This is the default. Reach for Tier 2 only when you need stagger or global timing control.

---

### Tier 2 — `.brx-animate` (opt-in, stagger and variable timing control)

Add `.brx-animate` when you need:
- **Stagger** across a group of children
- **CSS variable control** over duration/delay (global or per-element override)
- **Consistent timing** across a component without touching each interaction panel

**The two classes:**

| Class | Applied to | Purpose |
|---|---|---|
| `.brx-animate` | The animated element | Hooks into CSS variable cascade for timing |
| `.brx-animate--stagger-delay` | The parent container | Auto-increments delay per child |

### Single element with variable timing control
```
Element
  └─ Class:         brx-animate
  └─ Interaction:   Enter viewport → Start animation → fadeInUp
  └─ Duration:      blank  ← CSS var controls this
  └─ Delay:         blank  ← CSS var controls this
  └─ Run only once: ON
```

### Staggered group
```
Parent element
  └─ Class:       brx-animate--stagger-delay
  └─ No interaction on parent

Child elements (each)
  └─ Class:         brx-animate
  └─ Interaction:   Enter viewport → Start animation → fadeInUp
  └─ Duration:      blank
  └─ Delay:         0s  ← stagger CSS overrides via !important
  └─ Run only once: ON
```

---

## AVAILABLE ANIMATION NAMES

Custom keyframes that respect `--brx-transition-distance` (default: `20px`):

| Animation name | Direction |
|---|---|
| `fadeInUp` | From below |
| `fadeInDown` | From above |
| `fadeInLeft` | From left |
| `fadeInRight` | From right |

### Control travel distance
```css
.my-section {
  --brx-transition-distance: 40px;
}
```

---

## CSS VARIABLE CASCADE (TIER 2)

### How `--brx-animation-duration` resolves
```
.brx-animate.brx-animated
  └─ animation-duration: var(--brx-animation-duration)
       └─ set on .brx-animate as:
            var(--brx-animation-duration--base, 0.5s)
                 └─ override at ID/class level:
                      --brx-animation-duration--base: var(--brx-animation-duration--slow)
```

### Override variables (set at ID or scoping class level)

| Variable | Default | Purpose |
|---|---|---|
| `--brx-animation-duration--base` | `0.5s` | Base animation duration |
| `--brx-animation-delay-base` | `0.25s` | Base animation delay |
| `--brx-stagger-step-base` | `0.25s` | Increment between stagger steps |
| `--brx-animation-timing-function-base` | `ease-out` | Easing function |

### Global duration tokens (`:root`)
```
--brx-animation-duration--fast:    0.25s
--brx-animation-duration--medium:  0.5s
--brx-animation-duration--slow:    1s
--brx-animation-duration--slower:  2s
```

Reference them in overrides:
```css
#my-element {
  --brx-animation-duration--base: var(--brx-animation-duration--slow);
  --brx-animation-delay-base: 0.4s;
}
```

### Stagger timing override
```css
#my-stagger-parent {
  --brx-stagger-step-base: 0.15s;
  --brx-animation-delay-base: 0s;
}
```

---

## INTERACTION PANEL — WHAT TO SET / WHAT TO LEAVE

| Field | Tier 1 | Tier 2 |
|---|---|---|
| Trigger | ✅ Enter viewport | ✅ Enter viewport |
| Animation name | ✅ Set | ✅ Set |
| Duration | Set in panel | **blank** — CSS var controls |
| Delay | Set in panel | **blank** (stagger children: `0s`) |
| Target | Self | Self |
| Run only once | ✅ ON | ✅ ON |
| rootMargin | Optional | Optional |

> `0px 0px -50% 0px` triggers animation when 50% of the element is in view.

---

## MOVING `.brx-animate` INTO `global.css`

The `.brx-animate` CSS should live in `global.css`, not as a Bricks global class.
This makes it version-controlled, framework-independent, and always available site-wide.

### Steps

**1. Copy the full CSS block**
From Bricks > Global Classes, open `.brx-animate` and copy all CSS from the
`_cssCustom` field.

**2. Paste into `global.css`**
Place under a clearly labelled section header:
```css
/* ============================================================
   BRX ANIMATE — Bricks interaction animation utilities
   Tier 1: global fadeInUp keyframe override (no class needed)
   Tier 2: .brx-animate + .brx-animate--stagger-delay (opt-in)
   ============================================================ */
```

**3. Delete the Bricks global classes**
Once confirmed working in the browser, delete `.brx-animate` and
`.brx-animate--stagger-delay` from Bricks > Global Classes.

**4. Audit elements before deleting**
Elements that had `.brx-animate` applied as a Bricks global class will lose the
class when the global class is deleted — Bricks removes the reference, not just
the CSS. Before deleting:

- Audit all elements using `.brx-animate` in the canvas
- Re-apply it as a plain CSS class via the Bricks style panel (not a global class)

> In Bricks, a global class and a plain CSS class of the same name are functionally
> identical in the browser. Once the CSS lives in `global.css`, the class just needs
> to exist on the element — it does not need to be a registered global class.

---

## CONFIRMED PRODUCTION BEHAVIOUR

- `.brx-animated` is the state class Bricks adds on trigger. Confirm this in DevTools
  if animations aren't firing — if Bricks uses a different class name in your build,
  the duration/delay cascade won't apply.
- `!important` on stagger delay/timing is intentional — overrides Bricks' interaction
  panel inline values. Correct pattern, not a specificity hack.
- `prefers-reduced-motion` — ACSS collapses all animation durations to `0.01ms`
  globally. This system inherits that behaviour automatically.

---

## GOTCHAS

- **Tier 2 only: clear Duration and Delay in the panel** — the panel writes inline
  values that win over the CSS class. Leave both blank so the CSS variable cascade
  controls timing. The panel is for animation name and trigger only.
- **10-child stagger limit** — `:nth-child()` rules cover 1–10. Beyond that, children
  animate without incremented delay.
- **Do not mix Tier 1 and Tier 2 on the same element** — if `.brx-animate` is present,
  the panel duration/delay must be blank. If they're set, Tier 2 variable control is
  bypassed silently.
- **Variable name is double-dash before `base`** — `--brx-animation-duration--base`
  not `--brx-animation-duration-base`. A single-dash typo silently falls back to `0.5s`.

---

## REFERENCE FILES

| File | Purpose |
|---|---|
| `RULES.md` | Project coding conventions and output rules |
| `ACSS-AUTO.md` | What ACSS handles automatically |
| `ACSS-CHEATSHEET.md` | Utility classes and breakpoint syntax |
| `OUTPUT-MODES.md` | Bricks-ready vs Standalone preview modes |
| `GSAP.md` | GSAP animation patterns (when available) |

---

*BRX-ANIMATE.md v1.1 — updated March 2026*
*Tier 1 / Tier 2 split added. Migration to global.css instructions added.*
