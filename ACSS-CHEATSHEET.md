# ACSS-CHEATSHEET.md — Utility Class Reference
## All available ACSS 3.0 utility classes
Version: 1.0 | Source: acss-cheatsheet.html

> **Usage rule:** Utility classes are for one-off contextual changes only.
> Reusable components use custom BEM classes + `var()` tokens. See ACSS-AUTO.md.

---

## GRID

### Equal columns (fixed — require manual breakpoint handling)
```
grid--1  grid--2  grid--3  grid--4  grid--5  grid--6
grid--7  grid--8  grid--9  grid--10  grid--11  grid--12
```

### Fractional splits — fixed
```
grid--1-2  grid--2-1  grid--1-3  grid--3-1  grid--2-3  grid--3-2
```

### Auto-responsive equal (collapse automatically — preferred)
```
grid--auto-2  grid--auto-3  grid--auto-4  grid--auto-5  grid--auto-6
grid--auto-7  grid--auto-8  grid--auto-9  grid--auto-10  grid--auto-11  grid--auto-12
```

### Auto-responsive splits
```
grid--auto-1-2  grid--auto-2-1  grid--auto-1-3
grid--auto-3-1  grid--auto-2-3  grid--auto-3-2
```

### Grid rows
```
grid-rows--1  grid-rows--2  grid-rows--3
grid-rows--4  grid-rows--5  grid-rows--6
grid--auto-rows
```

### Grid flow modifiers
```
grid--stack-any   grid--auto-fill   grid--auto-fit
variable-grid
```

---

## COLUMN & ROW PLACEMENT

### Col span
```
col-span--1 through col-span--12   col-span--all
```

### Col start / end
```
col-start--1 through col-start--12
col-end--1 through col-end--12   col-end--last
```

### Row span / start / end
```
row-span--1 through row-span--6
row-start--1 through row-start--4
row-end--1 through row-end--4
```

### Order
```
order--first   order--last
```

---

## FLEXBOX
```
flex--row   flex--col   flex--row-reverse   flex--col-reverse
flex--wrap   flex--grow
self--start   self--center   self--end   self--stretch
```

---

## DISPLAY
```
display--block   display--inline   display--inline-block
display--inline-flex   display--contents   display--list-item
display--none
visibility--hidden   visibility--visible
```

---

## POSITION
```
relative   sticky   stretch   contain   isolation--isolate
breakout--full
sticky-top--s   sticky-top--m   sticky-top--l
```

---

## ALIGNMENT

### Justify content
```
justify-content--start   justify-content--center   justify-content--end
justify-content--between   justify-content--around   justify-content--stretch
```

### Align items
```
align-items--start   align-items--center   align-items--end
align-items--stretch   align-items--baseline
align-content--start   align-content--center   align-content--end
align-content--stretch   align-content--baseline
```

### Justify items
```
justify-items--start   justify-items--center
justify-items--end     justify-items--stretch
```

### Center helpers
```
center--all   center--x   center--y   center--self
center--top   center--bottom   center--left   center--right
```

---

## WIDTH

### Token-based (relative to content-width)
```
width--xs  width--s  width--m  width--l  width--xl  width--xxl
width--content   width--vp-max   width--full   width--auto
```

### Percentage-based
```
width--10  width--20  width--30  width--40  width--50
width--60  width--70  width--80  width--90
```

### Content width helpers
```
content-width        (max-width: var(--content-width), centered)
content-width--safe  (with safe inline padding)
```

---

## HEIGHT
```
height--20  height--30  height--40  height--50  height--60
height--70  height--80  height--90  height--100  height--full
max-height--20  max-height--40  max-height--60  max-height--80
max-height--100  max-height--full
```

---

## ASPECT RATIO
```
aspect--1-1   aspect--16-9   aspect--9-16   aspect--4-3   aspect--3-4
aspect--3-2   aspect--2-3   aspect--2-1   aspect--1-2
```

---

## OBJECT FIT POSITION
```
object-fit--top-left    object-fit--top-center    object-fit--top-right
object-fit--center-left                           object-fit--center-right
object-fit--bottom-left object-fit--bottom-center object-fit--bottom-right
```

---

## SECTION PADDING
> Applied automatically at M by default. Override with these classes on `<section>`.
```
section--xs   section--s   section--m   section--l
section--xl   section--xxl  section--none
```

---

## PADDING (general)
> Only use when ACSS contextual spacing doesn't cover the situation.
```
padding--xs   padding--s   padding--m   padding--l   padding--xl   padding--xxl
padding--none
```

---

## MARGIN
```
margin-top--xs    margin-top--s    margin-top--m    margin-top--l    margin-top--xl    margin-top--xxl
margin-bottom--xs margin-bottom--s margin-bottom--m margin-bottom--l margin-bottom--xl margin-bottom--xxl
margin-left--xs   margin-left--s   margin-left--m   margin-left--l   margin-left--xl   margin-left--xxl
margin-right--xs  margin-right--s  margin-right--m  margin-right--l  margin-right--xl  margin-right--xxl
margin-block--xs  margin-block--s  margin-block--m  margin-block--l  margin-block--xl  margin-block--xxl
margin-inline--xs margin-inline--s margin-inline--m margin-inline--l margin-inline--xl margin-inline--xxl
margin--none
```

---

## GAP
> Use contextual gap classes first: fr-grid-gap, fr-content-gap, fr-container-gap
```
gap--xs  gap--s  gap--m  gap--l  gap--xl  gap--xxl  gap--none
row-gap--xs  row-gap--s  row-gap--m  row-gap--l  row-gap--xl  row-gap--xxl
col-gap--xs  col-gap--s  col-gap--m  col-gap--l  col-gap--xl  col-gap--xxl
```

### Contextual gap (use these before generic gap)
```
fr-container-gap   (gap between containers inside a section)
fr-content-gap     (gap between content elements)
fr-grid-gap        (gap between grid items)
```

---

## BACKGROUND

### Contextual assignments (prefer these — trigger Auto Color Relationships)
```
bg--light    bg--dark    bg--ultra-light    bg--ultra-dark
bg--white    bg--black
```

### Brand colours
```
bg--primary  bg--primary-light  bg--primary-dark  bg--primary-hover
bg--primary-semi-light  bg--primary-semi-dark  bg--primary-ultra-light  bg--primary-ultra-dark
bg--base  bg--base-light  bg--base-dark  bg--base-hover
bg--base-semi-light  bg--base-semi-dark  bg--base-ultra-light  bg--base-ultra-dark
bg--neutral  bg--neutral-light  bg--neutral-dark
bg--neutral-semi-light  bg--neutral-semi-dark  bg--neutral-ultra-light  bg--neutral-ultra-dark
```

### Status colours
```
bg--danger  bg--danger-light  bg--danger-dark
bg--success  bg--success-light  bg--success-dark
bg--warning  bg--warning-light  bg--warning-dark
bg--info  bg--info-light  bg--info-dark
```

### Transparency — pattern: `bg--[colour]-trans-[10–90]`
```
bg--primary-trans-10  bg--primary-trans-30  bg--primary-trans-50
bg--primary-trans-70  bg--primary-trans-90
bg--neutral-trans-10  bg--neutral-trans-50
bg--base-trans-10     bg--base-trans-50
bg--white-trans-10    bg--white-trans-50
bg--black-trans-10    bg--black-trans-50
```

---

## OVERLAY
```
overlay--black-trans-10   overlay--black-trans-30   overlay--black-trans-50
overlay--black-trans-70   overlay--black-trans-90
overlay--white-trans-10   overlay--white-trans-30   overlay--white-trans-50
overlay--primary-trans-30 overlay--primary-trans-50
overlay--primary-dark-trans-50
overlay--neutral-trans-50   overlay--neutral-dark-trans-50
overlay--base-trans-50      overlay--base-dark-trans-50
```

---

## TEXT COLOUR

### Core contextual (prefer these)
```
text--dark   text--dark-muted   text--light   text--light-muted
text--black  text--white
```

### Brand — pattern: `text--[colour]-[shade]`
```
text--primary  text--primary-dark  text--primary-light
text--base     text--base-dark     text--base-light
text--neutral  text--neutral-dark  text--neutral-light
```

### Status
```
text--danger  text--success  text--warning  text--info
```

### Font weight (100–900)
```
text--100  text--200  text--300  text--400  text--500
text--600  text--700  text--800  text--900
text--bold
```

---

## TEXT SIZE
```
text--xs   text--s   text--m   text--l   text--xl   text--xxl
```

---

## TEXT STYLE & ALIGNMENT
```
text--left   text--center   text--right   text--justify
text--italic   text--oblique
text--uppercase   text--lowercase   text--capitalize   text--transform-none
text--underline   text--underline-dashed   text--underline-dotted
text--underline-double   text--underline-wavy
text--decoration-none   text--line-through   text--overline
text--pretty   balance   unbalance
line-clamp--1 through line-clamp--5
```

---

## HEADING SIZE CLASSES
> Use these when you need semantic heading at a different visual size.
```
h1   h2   h3   h4   h5   h6
```

---

## BORDER

### Full border (uses global border style)
```
border   border--light   border--dark
```

### Individual sides
```
border-top   border-top--light   border-top--dark
border-bottom  border-bottom--light  border-bottom--dark
border-left  border-left--light  border-left--dark
border-right  border-right--light  border-right--dark
border-block  border-block--light  border-block--dark
border-inline  border-inline--light  border-inline--dark
```

### Dividers
```
divider--all   divider-top   divider-bottom
```

---

## BORDER RADIUS
```
radius          (applies global --radius)
radius--xs  radius--s  radius--m  radius--l  radius--xl  radius--xxl
radius--50  radius--circle  radius--none
acss-auto-radius
```

---

## BOX SHADOW
```
box-shadow--m   box-shadow--l   box-shadow--xl
```

---

## OPACITY
```
opacity--0   opacity--5   opacity--10   opacity--15   opacity--20
opacity--25  opacity--30  opacity--35   opacity--40   opacity--45
opacity--50  opacity--55  opacity--60   opacity--65   opacity--70
opacity--75  opacity--80  opacity--85   opacity--90   opacity--95  opacity--100
```

---

## Z-INDEX
```
z--0   z--10  z--20  z--30  z--40  z--50
z--60  z--70  z--80  z--90
z--top   z--bottom
```

---

## MASONRY & COLUMN LAYOUT

### Masonry
```
masonry--1   masonry--2   masonry--3   masonry--4   masonry--5
```

### CSS multi-column count
```
col-count--1  col-count--2  col-count--3  col-count--4  col-count--5
col-width--s  col-width--m  col-width--l
col-gap--xs  col-gap--s  col-gap--m  col-gap--l  col-gap--xl  col-gap--xxl
col-rule--s  col-rule--m  col-rule--l  col-rule--solid  col-rule--dashed  col-rule--dotted
```

---

## LINK COLOUR
```
link--primary  link--primary-light  link--primary-dark
link--base  link--base-light  link--base-dark
link--neutral  link--black  link--white
link--danger  link--success  link--warning  link--info
link-hover--primary  link-hover--neutral  link-hover--black  link-hover--white
```

---

## HEADER HEIGHT UTILITIES
```
header--xs  header--s  header--m  header--l  header--xl  header--xxl
```

---

## MISC UTILITIES

### Flip / Transform
```
flip--x  flip--y  flip--xy  flip--both
```

### Fade edges
```
fade--top  fade--bottom  fade--left  fade--right
fade--block  fade--inline
```

### Forms
```
form--light   form--dark
```

### Misc
```
ribbon   ribbon--top-left   ribbon--top-right
transition
balance   unbalance   pretty
```

---

## BREAKPOINT MODIFIERS (confirmed from automatic.css)

Classes are inserted as breakpoint modifiers by adding `--[bp]-` between the class prefix and value.

| Breakpoint | Query | Modifier |
|---|---|---|
| xl | `max-width: 1366px` | `--xl-` |
| l | `max-width: 992px` | `--l-` |
| m | `max-width: 768px` | `--m-` |
| s | `max-width: 480px` | `--s-` |

### Classes with breakpoint modifiers supported
```
grid--[bp]-[1–12]          e.g. grid--l-2   grid--m-1
grid-rows--[bp]-[1–12]
section--[bp]-[xs|s|m|l|xl|xxl|none]
display--[bp]-[value]      e.g. display--m-none
aspect--[bp]-[ratio]
z--[bp]-[value]
```

### Grid examples
```
grid--3 grid--l-2 grid--m-1    (3→2→1 at breakpoints)
grid--4 grid--xl-2 grid--s-1   (4→2→1)
```

### Section padding examples
```
section--xxl section--xl-xl section--m-l
section--xl section--m-m section--s-s
```

### Grid alternate (zebra column reversal)
```
grid--alternate-xl  (fires at min-width: 1367px)
grid--alternate-l   (fires at min-width: 993px)
grid--alternate-m   (fires at min-width: 769px)
grid--alternate-s   (fires at min-width: 481px)
```
Apply to a wrapper; child `grid--2` rows will reverse column order on even rows.

---

## NOTES
- **Breakpoint modifiers confirmed** from automatic.css: xl=1366px, l=992px, m=768px, s=480px
- **Variables vs classes:** Every utility class has a `var()` equivalent. Use variables in custom BEM classes; use utility classes for one-off overrides only.
- **Auto Color Relationships** only fire on background utility classes (`.bg--dark` etc.), NOT on `var(--bg-dark)` in custom CSS.
- **Section classes** stay active even in Pro Mode — they're always appropriate.
- **Active button colour** is confirmed from the `:root` paste at session start — never assume `btn--primary` is active.

---

*Last updated: Session Feb 2026 | Source: acss-cheatsheet.html*
