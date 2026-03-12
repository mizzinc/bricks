# BRICKS-ELEMENTS.md — Element Reference & Component Identification
## Native Bricks elements, custom components, JSON schemas, and component selection rules
Version: 1.1 | Last updated: March 2026

---

## 1. COMPONENT IDENTIFICATION — WHEN BUILDING A SECTION

When building a section, identify which parts are **components** before writing any code. A component is any self-contained unit that:

1. **Appears in more than one parent** — stats in hero AND features, kicker across all sections, testimonial quote in multiple pages
2. **Has its own modifiers** — needs `--large`, `--light` variants independent of the parent section
3. **Has internal structure** — 3+ child elements with their own layout logic
4. **Maps to a recognisable UI pattern** — accordion, tabs, slider, counter, form, pricing table, toggle, countdown

If none of these apply, it stays as a BEM child of the section block. Don't preemptively extract — wait until the second usage before promoting a child to its own block.

---

## 2. COMPONENT RESOLUTION — BRICKS NATIVE FIRST

Once a component is identified, check whether Bricks provides a native element before building custom. Native elements handle accessibility, responsive behaviour, and interaction states that are expensive to recreate.

**Always use the native Bricks element when available:**

| Pattern | Bricks element | Don't build from |
|---|---|---|
| Animated number | `counter` | text-basic + JS |
| Collapsible content / FAQ | `bt-accordion` (custom) | Native `accordion-nested` is limited — use custom build. See Section 17 |
| Tabbed content | `tabs-nested` | divs + custom JS |
| Image/content slider | `slider-nested` | carousel + custom JS |
| Content toggle (dark/light, monthly/annual) | `toggle-mode` | checkbox + custom JS |
| Collapsible panel | `toggle` | div + custom JS |
| Star/score display | `rating` | SVG icons manually |
| Step-by-step list / feature list | `icon-list` | div + text-basic rows |
| Countdown timer | `countdown` | text-basic + JS |
| Animated text reveal | `anim-typing` | text-basic + JS |
| Progress indicator | `progress-bar` | div + width animation |
| Data comparison / pricing | `pricing-tables` | grid + manual markup |
| Image lightbox/gallery | `image-gallery` | image + custom modal |
| Contact/input collection | `form` | HTML inputs manually |
| Navigation menu | `nav-nestable` | list + custom links |
| Off-screen panel | `offcanvas` | fixed div + JS toggle |
| Breadcrumb trail | `breadcrumbs` | text-basic links |
| Team member cards | `team-members` | div + image + text |
| Testimonial display | `testimonials` | blockquote manually |
| Pie/donut chart | `pie-chart` | SVG + custom code |
| Back to top button | `back-to-top` | fixed anchor + JS |
| Dropdown content | `dropdown` | div + JS visibility |
| Alert/notice banner | `alert` | div + text-basic |
| Horizontal rule | `divider` | border-bottom on a div |
| Ordered/unordered list with meta | `list` | text-basic elements |
| Icon + text block | `icon-box` | div + SVG + text manually |
| Social media links | `social-icons` | div + SVG links manually |
| Google Map embed | `map` | iframe manually |
| OpenStreetMap embed | `map-leaflet` | iframe manually |

**Build custom when:**
- The native element doesn't support the design requirements (e.g. slider needs custom external nav — use native slider + custom nav components)
- No native element exists for the pattern
- The native element's markup or styling can't be overridden sufficiently

**When using native elements inside a section:**
- The native element still gets a BEM class scoped to the section: `bt-pricing__tabs` on a `tabs-nested`
- Section-level CSS overrides go in the section's root global class
- The native element handles its own internal behaviour — don't fight it

---

## 3. ELEMENT JSON SCHEMAS — GENERAL

Elements documented in RULES.md Section 2:
`section`, `container`, `block`, `div`, `heading`, `text-basic`, `text`, `text-link`, `counter`, `button`, `image`, `image-gallery`, `video`, `audio`, `svg`, `carousel`, `icon`, `icon-box`, `divider`, `breadcrumbs`, `social-icons`, `list`, `code`.

This file documents additional native elements not covered there, with confirmed JSON from production.

---

## 4. TABS — `tabs-nested`

Nestable tab component. Internal structure uses `_hidden._cssClasses` for Bricks' own class wiring — not `_cssGlobalClasses`.

### Structure
```
tabs-nested                          ← parent component
  block  (tab-menu)                  ← flex row of tab titles
    div  (tab-title)                 ← individual tab trigger
      text-basic                     ← tab label text
    div  (tab-title)
      text-basic
  block  (tab-content)               ← content pane wrapper
    block  (tab-pane)                ← individual pane
      text                           ← pane content (nestable — any elements)
    block  (tab-pane)
      text
```

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "tabs-nested",
  "parent": "xxxxxx",
  "children": ["xxxxxx", "xxxxxx"],
  "settings": {
    "titlePadding": {"top": 20, "right": 20, "bottom": 20, "left": 20},
    "titleActiveBackgroundColor": {"hex": "#dddedf"},
    "contentPadding": {"top": 20, "right": 20, "bottom": 20, "left": 20},
    "contentBorder": {
      "width": {"top": 1, "right": 1, "bottom": 1, "left": 1},
      "style": "solid"
    }
  }
}
```

### Internal element wiring
Tab menu and tab content blocks use `_hidden._cssClasses` — not global classes:
```json
{"settings": {"_direction": "row", "_hidden": {"_cssClasses": "tab-menu"}}, "label": "Tab menu"}
{"settings": {"_hidden": {"_cssClasses": "tab-title"}}, "label": "Title"}
{"settings": {"_hidden": {"_cssClasses": "tab-content"}}, "label": "Tab content"}
{"settings": {"_hidden": {"_cssClasses": "tab-pane"}}, "label": "Pane"}
```

**Do not modify `_hidden._cssClasses`** — these are Bricks internal. Add your BEM classes via `_cssGlobalClasses` alongside them.

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `titlePadding` | `{top, right, bottom, left}` | Padding on each tab title — integer px |
| `titleActiveBackgroundColor` | `{hex}` | Background of the active tab |
| `contentPadding` | `{top, right, bottom, left}` | Padding inside the content area — integer px |
| `contentBorder` | `{width: {top,right,bottom,left}, style}` | Border around content pane |

---

## 5. COUNTDOWN — `countdown`

Animated countdown timer to a target date/time.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "countdown",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "date": "2026-01-01 12:00",
    "fields": [
      {"format": "%D days", "id": "xxxxxx"},
      {"format": "%H hours", "id": "xxxxxx"},
      {"format": "%M minutes", "id": "xxxxxx"},
      {"format": "%S seconds", "id": "xxxxxx"}
    ],
    "gutter": {"top": 0, "right": 5, "bottom": 0, "left": 0}
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `date` | string | Target date — format `"YYYY-MM-DD HH:MM"` |
| `fields` | array | Each field has `format` string and unique `id` |
| `fields[].format` | string | Token + label — `%D`, `%H`, `%M`, `%S` |
| `gutter` | `{top, right, bottom, left}` | Spacing between fields — integer px |

### Format tokens

| Token | Output |
|---|---|
| `%D` | Days |
| `%H` | Hours |
| `%M` | Minutes |
| `%S` | Seconds |

---

## 6. ALERT — `alert`

Notification/notice banner with type variants.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "alert",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "content": "<p>I am an alert.</p>",
    "type": "info"
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `content` | string | HTML content string |
| `type` | string | `"info"`, `"success"`, `"warning"`, `"danger"` |

---

## 7. PIE CHART — `pie-chart`

Circular percentage chart.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "pie-chart",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "percent": 60,
    "content": "percent",
    "barColor": {"hex": "#03a9f4"},
    "trackColor": {"hex": "#f0f1f2"}
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `percent` | integer | Fill percentage (0–100) |
| `content` | string | What displays in centre — `"percent"` shows the number |
| `barColor` | `{hex}` | Fill colour |
| `trackColor` | `{hex}` | Background track colour |

---

## 8. PROGRESS BAR — `progress-bar`

Horizontal progress bars with labels and percentages.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "progress-bar",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "bars": [
      {"title": "Web design", "percentage": 80, "id": "xxxxxx"},
      {"title": "SEO", "percentage": 90, "id": "xxxxxx"}
    ],
    "showPercentage": true
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `bars` | array | Each bar has `title`, `percentage`, and unique `id` |
| `bars[].title` | string | Label text |
| `bars[].percentage` | integer | Fill percentage (0–100) |
| `showPercentage` | boolean | Display percentage number |

---

## 9. PRICING TABLES — `pricing-tables`

Pricing card layout with features list and CTA button.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "pricing-tables",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "_boxShadow": {
      "values": {"offsetX": 5, "offsetY": 10, "blur": 30, "spread": 0},
      "color": {"hex": "#212121", "hsl": "hsla(0, 0, 13%, 0.1)", "rgb": "rgba(33, 33, 33, 0.1)"}
    },
    "pricingTables": [
      {
        "title": "BUSINESS",
        "subtitle": "Subtitle goes here",
        "pricePrefix": "$",
        "price": "29",
        "priceSuffix": "90",
        "priceMeta": "per month",
        "features": "Unlimited websites\n20GB web space\nSSL certificate",
        "featuresIcon": {"library": "ionicons", "icon": "ion-ios-checkmark-circle-outline"},
        "buttonText": "GET STARTED",
        "buttonStyle": "primary",
        "id": "xxxxxx"
      }
    ]
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `pricingTables` | array | Each table is a pricing tier |
| `.title` | string | Plan name |
| `.subtitle` | string | Tagline below title |
| `.pricePrefix` | string | Currency symbol — `"$"` |
| `.price` | string | Main price value |
| `.priceSuffix` | string | Cents or additional — `"90"` |
| `.priceMeta` | string | Billing period — `"per month"` |
| `.features` | string | Newline-separated feature list (`\n`) |
| `.featuresIcon` | `{library, icon}` | Checklist icon (reference only — see SVG rule in RULES.md) |
| `.buttonText` | string | CTA button label |
| `.buttonStyle` | string | `"primary"`, `"secondary"` etc. |
| `_boxShadow` | object | Shadow with `values` and `color` |

---

## 10. LIST — `list`

Structured list with title and optional meta per item.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "list",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "items": [
      {"title": "List item #1", "meta": "$10.00", "id": "xxxxxx"},
      {"title": "List item #2", "meta": "$25.00", "id": "xxxxxx"}
    ]
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `items` | array | Each item has `title`, optional `meta`, and unique `id` |
| `.title` | string | Item label |
| `.meta` | string | Right-aligned meta text (price, date, etc.) |

---

## 11. DIVIDER — `divider`

Horizontal rule / separator.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "divider",
  "parent": "xxxxxx",
  "children": [],
  "settings": {}
}
```

No required settings — styled via ACSS or global class CSS.

---

## 12. ICON BOX — `icon-box`

Icon + heading + text block as a single element.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "icon-box",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "icon": {"library": "themify", "icon": "ti-wordpress"},
    "content": "<h4>Icon box heading</h4><p>Description text here.</p>"
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `icon` | `{library, icon}` | Icon reference (see SVG rule — prefer inline SVG where possible) |
| `content` | string | Raw HTML — heading + paragraph tags inline |

> **Note:** `icon-box` uses the icon library system internally. For new builds where performance matters, consider building the same layout from `div` + inline SVG + `heading` + `text-basic` with BEM classes. Use `icon-box` when rapid prototyping or when the icon library is already loaded for other reasons.

---

## 13. SOCIAL ICONS — `social-icons`

Social media icon links. Colours are set via inline hex in settings — not via ACSS tokens or global classes.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "social-icons",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "_padding": {"top": 15, "right": 15, "bottom": 15, "left": 15},
    "_typography": {"color": {"hex": "#ffffff"}},
    "icons": [
      {
        "label": "X",
        "icon": {"library": "fontawesomeBrands", "icon": "fab fa-x-twitter"},
        "background": {"hex": "#4cc2ff"},
        "id": "xxxxxx"
      },
      {
        "label": "Facebook",
        "icon": {"library": "fontawesomeBrands", "icon": "fab fa-facebook-square"},
        "background": {"hex": "#3b5998"},
        "id": "xxxxxx"
      },
      {
        "label": "Instagram",
        "icon": {"library": "fontawesomeBrands", "icon": "fab fa-instagram"},
        "background": {"hex": "#4E433C"},
        "id": "xxxxxx"
      }
    ]
  }
}
```

### Settings reference

| Setting | Type | Notes |
|---|---|---|
| `_padding` | `{top, right, bottom, left}` | Icon padding — integer px |
| `_typography.color` | `{hex}` | Icon colour |
| `icons` | array | Each icon has `label`, `icon`, `background`, and unique `id` |
| `.label` | string | Accessible label / platform name |
| `.icon` | `{library, icon}` | Font Awesome Brands reference |
| `.background` | `{hex}` | Background colour per icon |

> **Note:** `social-icons` is one of the few elements where icon library references are acceptable — the element requires them internally. For custom social icon layouts, build from `div` + inline SVG instead.

---

## 14. BREADCRUMBS — `breadcrumbs`

Auto-generated breadcrumb trail. No required settings.

### JSON schema
```json
{
  "id": "xxxxxx",
  "name": "breadcrumbs",
  "parent": "xxxxxx",
  "children": [],
  "settings": {}
}
```

---

## 15. MAPS

Three distinct map elements available.

### Google Map — `map`
```json
{
  "id": "xxxxxx",
  "name": "map",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "addresses": [
      {"latitude": "52.5164154966524", "longitude": "13.377643715349544"}
    ],
    "scrollwheel": true,
    "draggable": true,
    "streetViewControl": true,
    "zoomControl": true
  }
}
```

| Setting | Type | Notes |
|---|---|---|
| `addresses` | array | Each marker has `latitude` and `longitude` as strings |
| `scrollwheel` | boolean | Scroll zoom |
| `draggable` | boolean | Pan by drag |
| `streetViewControl` | boolean | Street View toggle |
| `zoomControl` | boolean | Zoom buttons |

### Leaflet / OpenStreetMap — `map-leaflet`
```json
{
  "id": "xxxxxx",
  "name": "map-leaflet",
  "parent": "xxxxxx",
  "children": [],
  "settings": {
    "layers": [
      {"name": "OpenStreetMap", "url": "https://tile.openstreetmap.org/{z}/{x}/{y}.png"}
    ],
    "boxZoom": true,
    "zoomControl": true,
    "attributionControl": true,
    "closePopupOnClick": true,
    "dragging": true,
    "trackResize": true
  }
}
```

| Setting | Type | Notes |
|---|---|---|
| `layers` | array | Tile layer with `name` and `url` |
| `boxZoom` | boolean | Zoom by box selection |
| `zoomControl` | boolean | Zoom buttons |
| `attributionControl` | boolean | Show attribution |
| `dragging` | boolean | Pan by drag |

### Map Connector — `map-connector`
```json
{
  "id": "xxxxxx",
  "name": "map-connector",
  "parent": "xxxxxx",
  "children": [],
  "settings": {}
}
```

Links Google Map and Leaflet map elements. No required settings.

---

## 16. CUSTOM COMPONENTS

Custom-built components that replace or extend native Bricks elements. These use standard Bricks elements (`block`, `div`, `heading`, `text`) with custom JS in a `code` element.

---

### 17. ACCORDION — `bt-accordion` (custom)

Custom accordion built from standard Bricks elements. Replaces the native `accordion-nested` which is limited in layout control, schema support, and styling flexibility.

#### Why custom over native
- Full control over header layout (grid with asymmetric columns)
- Built-in FAQ structured data (`application/ld+json`) via `bt-faq-schema` attribute
- Configurable behaviour via data attributes — no JS editing needed per instance
- Semantic `ul` > `li` structure
- Accessible: `role="button"`, `tabindex`, `aria-expanded` on headers; `role="region"` on body

#### Structure
```
block (ul)         bt-accordion                    ← parent, holds config attributes
  block (li)       bt-accordion__item              ← repeating item
    block          bt-accordion__header            ← click target, grid layout
      heading      bt-accordion__title             ← h3 (or appropriate level)
      block        bt-accordion__header-wrap       ← flex row: subtitle + icon
        text-basic                                 ← subtitle / meta text
        div        fr-accordion__icon-wrapper      ← plus/minus icon container
          div      faqs__toggle-icon               ← CSS-only plus/minus
    block          bt-accordion__body              ← collapsible container (height: 0)
      block        bt-accordion__wrap              ← grid layout matching header
        text       bt-accordion__text              ← rich text content
```

#### Configuration attributes

Set on the parent `bt-accordion` element via `_attributes`:

| Attribute | Type | Default | Purpose |
|---|---|---|---|
| `bt-expand-first` | `"true"` / `"false"` | `"true"` | Expand first item on page load |
| `bt-expand-all` | `"true"` / `"false"` | `"false"` | Expand all items on page load |
| `bt-close-siblings` | `"true"` / `"false"` | `"true"` | Collapse other items when one opens |
| `bt-header-scroll` | integer string | `"767"` | Viewport width (px) at or below which clicking scrolls to the opened item |
| `bt-faq-schema` | `"true"` / `"false"` | `"true"` | Inject FAQ structured data into `<head>` |
| `bt-transition-duration` | integer string | `"300"` | Expand/collapse transition duration in ms |

#### Global classes

All CSS lives in the `bt-accordion` root global class. Child classes have empty settings.

| Global class ID | Name | Notes |
|---|---|---|
| `nfodwp` | `bt-accordion` | Root — holds all CSS |
| `goecaj` | `bt-accordion__item` | Empty |
| `iwmllr` | `bt-accordion__header` | Empty |
| `zekpuv` | `bt-accordion__title` | Empty |
| `hbclzh` | `bt-accordion__header-wrap` | Empty |
| `cvyxmw` | `bt-accordion__icon-wrapper` | Empty — hidden, legacy SVG icon |
| `ozldrq` | `bt-accordion__icon` | Empty — legacy SVG icon |
| `fdlkkr` | `faqs__icons` | Has mobile CSS — legacy Frames class |
| `zdprpj` | `faqs__toggle-icon` | Has CSS — plus/minus icon, legacy Frames class |
| `jrwqym` | `bt-accordion__body` | Empty |
| `fuczdv` | `bt-accordion__wrap` | Empty |
| `abfnip` | `bt-accordion__text` | Empty |

> **Legacy naming:** `faqs__icons`, `faqs__toggle-icon`, and `fr-accordion__icon-wrapper` are Frames framework classes carried over. They don't follow `bt-` BEM convention. On new builds, rename to `bt-accordion__icon-wrap` and `bt-accordion__toggle-icon` and migrate the CSS into the root global class.

#### CSS (root global class: `bt-accordion`)

```css
/* Global class: bt-accordion */

/* ── Item ── */
%root%__item {
  border-block-end: 1px solid var(--primary);
}

%root%__item:first-of-type {
  border-block-start: 1px solid var(--primary);
}

%root%__item.active %root%__icon {
  transform: rotate(180deg);
}

%root%__item.active %root%__header {
  border-color: var(--primary);
}

%root%__item.active %root%__title {
  color: var(--primary);
}

/* ── Header ── */
%root%__header {
  position: relative;
  display: grid;
  grid-template-columns: var(--grid-2-3);
  align-items: center;
  padding-block: var(--space-xs);
  cursor: pointer;
}

@media (max-width: 768px) {
  %root%__header {
    display: flex;
    align-items: flex-start;
  }
}

/* ── Title ── */
%root%__title {
  font-size: calc(var(--text-xl) + 0.5rem); /* FLAG: confirm scale */
  font-family: span; /* FLAG: confirm typeface */
  font-weight: 300;
}

/* ── Header wrap ── */
%root%__header-wrap {
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

/* ── Icon ── */
%root%__icon {
  font-size: 1.6rem;
  color: var(--text-dark);
  transition: transform .4s ease;
}

/* ── Body ── */
%root%__body {
  overflow: hidden;
  height: 0px;
  transition: height .4s ease;
}

/* ── Body wrap ── */
%root%__wrap {
  display: grid;
  grid-template-columns: var(--grid-2-3);
  justify-content: space-between;
  padding-block-end: var(--space-xl);
}

@media (max-width: 768px) {
  %root%__wrap {
    display: flex;
  }
}

/* ── Text ── */
%root%__text {
  grid-column-start: 2;
  font-weight: 300;
}
```

#### Plus/minus toggle icon CSS (legacy — `faqs__toggle-icon`)

```css
.faqs__toggle-icon {
  height: 16px;
  position: relative;
  width: 16px;
}

.faqs__toggle-icon::before,
.faqs__toggle-icon::after {
  background-color: var(--primary);
  content: "";
  position: absolute;
  transition: transform 0.25s ease-out;
}

.faqs__toggle-icon::before {
  height: 16px;
  left: 50%;
  margin-left: -1px;
  top: 0;
  width: 1px;
}

.faqs__toggle-icon::after {
  height: 1px;
  left: 0;
  margin-top: -1px;
  top: 50%;
  width: 16px;
}

.bt-accordion__item.active .faqs__toggle-icon::before {
  transform: rotate3d(0, 0, 1, 90deg);
}

@media (max-width: 767px) {
  .faqs__toggle-icon {
    height: 24px;
    width: 24px;
  }
  .faqs__toggle-icon::before {
    height: 24px;
  }
  .faqs__toggle-icon::after {
    width: 24px;
  }
}
```

#### JavaScript — `code` element

The accordion JS lives in a Bricks `code` element at root level (`parent: 0`). It initialises all `.bt-accordion` instances on the page, reads config from data attributes, and handles expand/collapse, keyboard interaction, scroll-to-item, and FAQ schema injection.

Full script: see `bt-accordion` code element in the live project. Key behaviours:

- **Expand/collapse**: sets `height: scrollHeight` on body, toggles `.active` on item
- **Keyboard**: Enter and Space trigger toggle on headers
- **Scroll**: at viewport widths ≤ `bt-header-scroll`, scrolls to opened item after transition
- **FAQ schema**: generates `FAQPage` structured data from question/answer content and injects into `<head>`
- **Builder compatibility**: includes `<style>` block setting `[data-v-app] .bt-accordion__body { height: auto; }` so content is visible in the Bricks editor

#### Bricks JSON — parent element

```json
{
  "id": "xxxxxx",
  "name": "block",
  "parent": "xxxxxx",
  "children": ["xxxxxx", "xxxxxx", "xxxxxx"],
  "settings": {
    "tag": "ul",
    "_cssGlobalClasses": ["nfodwp", "acss_import_list--none"],
    "_attributes": [
      {"id": "xxxxxx", "name": "bt-expand-first", "value": "true"},
      {"id": "xxxxxx", "name": "bt-expand-all", "value": "false"},
      {"id": "xxxxxx", "name": "bt-close-siblings", "value": "true"},
      {"id": "xxxxxx", "name": "bt-header-scroll", "value": "767"},
      {"id": "xxxxxx", "name": "bt-faq-schema", "value": "true"}
    ]
  },
  "label": "Accordion"
}
```

#### Bricks JSON — item element

```json
{
  "id": "xxxxxx",
  "name": "block",
  "parent": "xxxxxx",
  "children": ["xxxxxx", "xxxxxx"],
  "settings": {
    "tag": "li",
    "_cssGlobalClasses": ["goecaj"]
  },
  "label": "Item"
}
```

#### Bricks JSON — header element

```json
{
  "id": "xxxxxx",
  "name": "block",
  "parent": "xxxxxx",
  "children": ["xxxxxx", "xxxxxx"],
  "settings": {
    "_cssGlobalClasses": ["iwmllr"],
    "_attributes": [
      {"id": "xxxxxx", "name": "tabindex", "value": "0"},
      {"id": "xxxxxx", "name": "role", "value": "button"},
      {"id": "xxxxxx", "name": "aria-expanded", "value": "false"}
    ]
  },
  "label": "Header"
}
```

#### Bricks JSON — body element

```json
{
  "id": "xxxxxx",
  "name": "block",
  "parent": "xxxxxx",
  "children": ["xxxxxx"],
  "settings": {
    "_cssGlobalClasses": ["jrwqym"],
    "_attributes": [
      {"id": "xxxxxx", "name": "role", "value": "region"}
    ]
  },
  "label": "Body"
}
```

---

## 18. ELEMENTS PENDING DOCUMENTATION

The following elements require JSON export to document:

- `slider-nested` — nestable slider (see SLIDERS.md for integration patterns)
- `toggle` — collapsible panel
- `toggle-mode` — content toggle (e.g. monthly/annual)
- `nav-nestable` — navigation menu
- `offcanvas` — off-screen panel
- `dropdown` — dropdown content
- `form` — contact/input form
- `rating` — star/score display
- `icon-list` — icon + text list
- `anim-typing` — animated text reveal
- `team-members` — team member cards
- `testimonials` — testimonial display
- `back-to-top` — back to top button

Export a configured sample from Bricks and paste the JSON here to document.

---

## REFERENCE FILES

| File | Purpose |
|---|---|
| `RULES.md` | Project coding conventions — element mapping in Section 2 |
| `ACSS-AUTO.md` | What ACSS handles automatically |
| `SLIDERS.md` | Splide slider integration patterns |
| `BRX-ANIMATE.md` | Bricks native animation system |

---

*BRICKS-ELEMENTS.md v1.1 — March 2026*
*v1.0: Component identification rules + 15 native element schemas from production JSON.*
*v1.1: Added custom components section. bt-accordion documented with full structure, CSS, JS reference, and config attributes.*
