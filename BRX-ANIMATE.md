# BRX-ANIMATE.md — Bricks Native Interaction Animation Utilities
## Global animation helper classes for Bricks Builder interactions
Version: 1.0 | Production-confirmed | Works alongside native Bricks interaction system

---

## OVERVIEW

This system provides two global utility classes that extend Bricks' native interaction panel with controlled, token-driven animation behaviour. It does **not** replace Bricks interactions — it wraps them with CSS variable control so duration, delay, timing, and stagger can be managed globally and overridden per-element without touching each interaction individually.

**Live in:** Global CSS (Bricks > Settings > Custom Code, or a dedicated global class)  
**Dependencies:** Bricks native interactions (Enter viewport → Start animation)  
**No JavaScript required** — pure CSS cascade.

---

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
conflicting animation-duration and animation-delay values.

> GSAP patterns are documented separately. See `GSAP.md` when available.

---

## THE TWO CLASSES

| Class | Applied to | Purpose |
|---|---|---|
| `.brx-animate` | The **element receiving the animation** | Sets up the CSS variable cascade for duration, delay, and timing |
| `.brx-animate--stagger-delay` | The **parent container** | Auto-increments delay for each child via `:nth-child()` |

---

## CLASS 1 — `.brx-animate`

Applied directly to any element that has a Bricks interaction set on it.

### What it does

Sets up a local CSS variable cascade so animation duration, delay, and timing-function can be controlled by:
1. The global `:root` defaults (`--brx-animation-duration--fast`, `--brx-animation-duration--medium`, etc.)
2. Per-component overrides at ID or unique class level via `--brx-animation-duration-base`, `--brx-animation-delay-base`, etc.

The `.brx-animate.brx-animated` compound selector fires when Bricks adds the `.brx-animated` state class on interaction trigger, applying the resolved duration and delay.

### Override variables (set at ID or parent class level)

| Variable | Default | Purpose |
|---|---|---|
| `--brx-animation-duration-base` | `0.5s` | Base animation duration |
| `--brx-animation-delay-base` | `0.25s` | Base animation delay |
| `--brx-stagger-step-base` | `0.25s` | Increment between stagger steps |
| `--brx-animation-timing-function-base` | `ease-out` | Easing function |

### Global duration tokens (set in `:root`)
```
--brx-animation-duration--fast:    0.25s
--brx-animation-duration--medium:  0.5s
--brx-animation-duration--slow:    1s
--brx-animation-duration--slower:  2s
```

### Per-element override pattern
```css
/* Set on the element's ID or a scoping class — NOT in the Bricks interaction panel */
#my-element-id {
  --brx-animation-duration-base: var(--brx-animation-duration--slow);
  --brx-animation-delay-base: 0.4s;
}
```

---

## CLASS 2 — `.brx-animate--stagger-delay`

Applied to the **parent container** of a group of animated children.

### What it does

Uses `:nth-child()` counters to automatically increment `--brx-animation-delay-x` for each child. The `!important` on `animation-delay` ensures it overrides whatever delay is set in the Bricks interaction panel.

Supports up to **10 children**. Beyond 10, stagger is not auto-incremented.

### Stagger delay formula
```
Child 1:  delay = base-delay
Child 2:  delay = stagger-step + base-delay
Child N:  delay = ((N-1) × stagger-step) + base-delay
```

### Override stagger timing at component level
```css
#my-stagger-parent {
  --brx-stagger-step-base: 0.15s;   /* tighter stagger */
  --brx-animation-delay-base: 0s;   /* no initial offset */
}
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

## INTERACTION PANEL SETUP

### Single element (no stagger)
```
Element classes:  brx-animate
Trigger:          Enter viewport
Action:           Start animation
Animation:        fadeInUp (or whichever direction)
Duration:         Set here OR leave and control via CSS var
Delay:            Set here OR leave and control via CSS var
Run only once:    ON
```

### Staggered group
```
Parent element
  └─ Classes:       brx-animate--stagger-delay
  └─ No interaction on parent

Child elements (each)
  └─ Classes:       brx-animate
  └─ Trigger:       Enter viewport → Start animation → fadeInUp
  └─ Duration:      Leave blank (CSS var controls it) or set to override
  └─ Delay:         0s  ← stagger CSS overrides this via !important
  └─ Run only once: ON
```

> **Why 0s in the panel?** `.brx-animate--stagger-delay` overrides `animation-delay` with `!important` per child. The panel value is irrelevant once this class is on the parent — set it to `0s` to avoid confusion.

---

## SLIDER STAGGER

The same stagger logic applies to Bricks/Splide slider slides:
```
Slider element
  └─ Classes:  brxe-fr-slider brx-animate--stagger-delay

Each slide
  └─ Classes:  brx-animate
  └─ Interaction: Enter viewport → Start animation
```

---

## WHAT TO SET / WHAT TO LEAVE IN THE PANEL

| Field | Panel | CSS variable |
|---|---|---|
| Trigger | ✅ Always set | — |
| Animation name | ✅ Always set | — |
| Duration | Optional override | `--brx-animation-duration-base` on ID/class |
| Delay | **0s when using stagger** | `--brx-animation-delay-base` on parent ID |
| Target | Self (default) | — |
| Run only once | ✅ ON | — |
| rootMargin | Optional — `0px 0px -50% 0px` for 50% in-view trigger | — |

---

## CONFIRMED PRODUCTION BEHAVIOUR

- `.brx-animated` is the state class Bricks adds on trigger. If animations aren't firing, confirm this class name in DevTools — if Bricks uses a different class in your build version, the duration/delay cascade won't apply.
- `!important` on stagger delay/timing is intentional — it overrides Bricks' interaction panel inline values. Correct pattern, not a specificity hack.
- In raw CSS source, `%root%` is Bricks global class self-reference. When registered as a Bricks global class, `%root%` resolves to `.brx-animate` or `.brx-animate--stagger-delay` respectively.

---

## GOTCHAS

- **Do not set Duration/Delay in the panel AND via CSS variables** — the panel writes inline values that override the class. Use one or the other.
- **10-child stagger limit** — `:nth-child()` rules cover 1–10. Beyond that, children animate without incremented delay.
- **`prefers-reduced-motion`** — ACSS collapses all animation durations to `0.01ms` globally. This system inherits that. No extra work needed.
- **Panel sets Duration to `1s` by default** (as shown in screenshot). If relying on CSS var control, clear this field or it will override the var.

---

## REFERENCE FILES

| File | Purpose |
|---|---|
| `RULES.md` | Project coding conventions and output rules |
| `ACSS-AUTO.md` | What ACSS handles automatically |
| `ACSS-CHEATSHEET.md` | Utility classes and breakpoint syntax |
| `OUTPUT-MODES.md` | Bricks-ready vs Standalone preview modes |

---

*BRX-ANIMATE.md v1.0 — ingested from production CSS, March 2026*  
*Source: `.brx-animate` and `.brx-animate--stagger-delay` global class CSS*
```

---

**To add to the project:** create a new file called `BRX-ANIMATE.md` in your project knowledge and paste the above. Then update the reference table in `RULES.md` Section 15 with this row:
```
| `BRX-ANIMATE.md` v1.0 | Bricks interaction animation utilities — `.brx-animate` + stagger system |
