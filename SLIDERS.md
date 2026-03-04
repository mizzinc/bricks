# SLIDERS.md
# Bricks Native Nestable Slider — Splide Integration
# Reference implementation: bt-carousel-component2
# Last updated: March 2026

---

## 1. ARCHITECTURE OVERVIEW

Bricks' nestable slider (introduced v1.5) is powered by SplideJS. All Splide instances are stored at `window.bricksData.splideInstances` and keyed by the Bricks **element ID** (not CSS ID).

The `bt-carousel-component2` code element is a reusable integration script that adds custom external navigation (prev/next buttons), progress bars, fraction counters, and slider syncing to any Bricks nestable slider on the page. It identifies sliders by a shared `bt-data-slider` attribute.

### How it works

1. Script runs on `DOMContentLoaded` (with 250ms delay for Bricks init)
2. Finds all slider elements matching the class array (`sliderClasses`)
3. Looks up each slider's Splide instance from `bricksData.splideInstances`
4. Wires up external controls, progress bars, and fractions via `bt-data-*` attributes
5. Handles sync between main + thumbnail sliders

---

## 2. ACCESSING THE SPLIDE INSTANCE

### By element ID (preferred)

```js
const mySlider = window.bricksData?.splideInstances['rrbqsp'] || false;
```

The element ID is the 6-char alphanumeric ID visible in the Bricks structure panel or in the rendered HTML as `id="brxe-rrbqsp"`. **Not** the CSS ID you assign manually.

### By DOM element (fallback)

When the ID isn't known ahead of time, iterate instances and match by root element:

```js
const allInstances = window.bricksData?.splideInstances;
let mySlider = false;

for (const key in allInstances) {
  if (allInstances[key].root === sliderElement) {
    mySlider = allInstances[key];
    break;
  }
}
```

### By element ID from DOM

The `bt-carousel-component2` pattern uses the slider element's `id` attribute (which Bricks sets as `brxe-{elementId}`) and looks it up:

```js
const sliderId = sliderElement.id;          // e.g. "brxe-rrbqsp"
// Try direct match first (some versions key by full ID)
let mySlider = allInstances[sliderId] || false;
// Fallback to root element match
if (!mySlider) {
  for (const key in allInstances) {
    if (allInstances[key].root === sliderElement) {
      mySlider = allInstances[key];
      break;
    }
  }
}
```

---

## 3. DATA ATTRIBUTE LINKING SYSTEM

All external controls link to their slider via a shared `bt-data-slider` value. This allows multiple independent sliders on one page, each with their own controls.

### Attributes

| Attribute | On element | Purpose |
|---|---|---|
| `bt-data-slider="portfolio"` | Slider element | Identifies this slider |
| `bt-data-slider="portfolio"` | Prev/next buttons | Links buttons to slider |
| `bt-data-slider="portfolio"` | Progress bar | Links progress to slider |
| `bt-data-slider="portfolio"` | Fractions element | Links fractions to slider |
| `bt-data-progress-type="progress"` | Progress bar | Standard fill progress |
| `bt-data-progress-type="animated"` | Progress bar | Autoplay-synced animated bar |
| `bt-data-fractions="true"` | Fractions element | Enables fraction counter |
| `bt-data-sync="main"` | Main slider | Marks as sync source |
| `bt-data-sync="portfolio"` | Thumbnail slider | Links to main slider's bt-data-slider value |

### Bricks implementation

Set these via the element's Custom Attributes panel:
```
Name: bt-data-slider     Value: portfolio
Name: bt-data-sync       Value: main
```

---

## 4. EXTERNAL NAVIGATION (PREV / NEXT)

### BEM classes

```
bt-slidernav-prev   → Previous button
bt-slidernav-next   → Next button
```

Both must also carry `bt-data-slider="[name]"` to link to the correct slider.

### Wiring

```js
const myPrevBtn = document.querySelector(
  `.bt-slidernav-prev[bt-data-slider="${sliderElement.getAttribute('bt-data-slider')}"]`
);
const myNextBtn = document.querySelector(
  `.bt-slidernav-next[bt-data-slider="${sliderElement.getAttribute('bt-data-slider')}"]`
);

myPrevBtn.addEventListener('click', function() {
  mySlider.go('-1');
});
myNextBtn.addEventListener('click', function() {
  mySlider.go('+1');
});
```

### Button state (disabled at boundaries)

For non-loop sliders, disable buttons at first/last position:

```js
const updateButtonStates = () => {
  const totalSlides = mySlider.length;
  const slidesPerPage = mySlider.options.perPage || 1;
  const lastSlideIndex = totalSlides - slidesPerPage;

  if (mySlider.options.type === 'loop') {
    // Loop sliders never disable
    myPrevBtn.disabled = false;
    myNextBtn.disabled = false;
  } else {
    myPrevBtn.disabled = mySlider.index === 0;
    myNextBtn.disabled = mySlider.index >= lastSlideIndex;
  }
};

mySlider.on('mounted moved', updateButtonStates);
updateButtonStates();
```

### CSS for disabled state

```css
%root%__nav-btn:disabled {
  opacity: 0.25;
  cursor: default;
  pointer-events: none;
}
```

---

## 5. PROGRESS BAR

Two types supported: standard fill and autoplay-synced animated.

### Standard progress (`bt-data-progress-type="progress"`)

Fills proportionally based on current slide position:

```js
const updateProgressBar = () => {
  const end = mySlider.Components.Controller.getEnd() + 1;
  const rate = Math.min((mySlider.index + 1) / end, 1);
  progressBar.style.width = String(100 * rate) + '%';
};

mySlider.on('mounted move', updateProgressBar);
updateProgressBar();
```

### Animated progress (`bt-data-progress-type="animated"`)

Syncs to autoplay interval — fills over the duration, resets on slide change:

```js
const interval = mySlider.options.interval || 3000;

function startProgressBar() {
  progressBar.style.transitionDuration = interval + 'ms';
  progressBar.style.width = '100%';
}

function resetProgressBar() {
  progressBar.style.transitionDuration = '0ms';
  progressBar.style.width = '0%';
  setTimeout(startProgressBar, 10);
}

mySlider.on('autoplay:playing', startProgressBar);
mySlider.on('move', resetProgressBar);
startProgressBar();
```

### BEM class

```
bt-slider-progressbar   → Progress bar element
```

Must carry `bt-data-slider="[name]"` and `bt-data-progress-type="progress|animated"`.

### CSS for progress bar

```css
.bt-slider-progressbar {
  height: 4px;
  width: 0;
  background: var(--neutral);
  transition: width 0.3s ease;
}
```

The parent wrapper provides the track background colour.

---

## 6. FRACTION COUNTER

Displays current/total slide count (e.g. "3/12"):

```js
const fractionsElement = document.querySelector(
  `[bt-data-slider="${sliderElement.getAttribute('bt-data-slider')}"][bt-data-fractions="true"]`
);

if (fractionsElement) {
  const updateFractions = () => {
    const currentIndex = mySlider.index + 1;
    const totalSlides = mySlider.length;
    fractionsElement.innerText = `${currentIndex}/${totalSlides}`;
  };

  mySlider.on('mounted move', updateFractions);
  updateFractions();
}
```

---

## 7. SLIDER SYNC (MAIN + THUMBNAILS)

Two sliders can be synced so clicking a thumbnail navigates the main slider.

### Data attributes

```
Main slider:      bt-data-sync="main"       bt-data-slider="portfolio"
Thumbnail slider: bt-data-sync="portfolio"   (matches main's bt-data-slider value)
```

### Sync logic

```js
const syncInit = (mainSliderId, thumbSliderId) => {
  const main = bricksData.splideInstances[mainSliderId];
  const thumbnail = bricksData.splideInstances[thumbSliderId];
  main.sync(thumbnail);
};

const mainSliders = document.querySelectorAll('[bt-data-sync="main"]');
mainSliders.forEach(mainSlider => {
  const syncValue = mainSlider.getAttribute('bt-data-slider');
  const thumbSliders = document.querySelectorAll(`[bt-data-sync="${syncValue}"]`);

  thumbSliders.forEach(thumbSlider => {
    const mainId = mainSlider.dataset.scriptId;
    const thumbId = thumbSlider.dataset.scriptId;

    setTimeout(() => {
      syncInit(mainId, thumbId);
    }, 0);
  });
});
```

**Important:** `sync()` must be called before `mount()` in standard Splide, but since Bricks already mounts sliders, the `setTimeout(fn, 0)` defers the sync call to after the current execution context.

---

## 8. KEY SPLIDE API REFERENCE

### Properties

| Property | Type | Description |
|---|---|---|
| `mySlider.index` | number | Current slide index (0-based) |
| `mySlider.length` | number | Total number of slides |
| `mySlider.options` | object | Current options (can set responsive options) |
| `mySlider.root` | Element | Root DOM element of the slider |
| `mySlider.Components` | object | All component objects |

### Methods

| Method | Description |
|---|---|
| `mySlider.go('+1')` | Next slide |
| `mySlider.go('-1')` | Previous slide |
| `mySlider.go(3)` | Go to slide index 3 |
| `mySlider.go('>')` | Next page (respects perPage) |
| `mySlider.go('<')` | Previous page (respects perPage) |
| `mySlider.on('event', fn)` | Register event handler |
| `mySlider.off('event')` | Remove event handler |
| `mySlider.refresh()` | Reinitialise (use after DOM changes) |
| `mySlider.destroy()` | Destroy instance |
| `mySlider.sync(otherSplide)` | Sync two sliders (call before mount) |

### Events

| Event | Fires when |
|---|---|
| `mounted` | All components mounted (register before mount) |
| `move` | Right before the carousel moves |
| `moved` | Right after the carousel moves |
| `active` | A slide becomes active |
| `inactive` | A slide becomes inactive |
| `click` | User clicks any slide |
| `resize` | Window is resized |
| `autoplay:playing` | Autoplay is running |
| `autoplay:pause` | Autoplay pauses |

### Controller component

```js
const end = mySlider.Components.Controller.getEnd();
// Returns the last reachable index
// e.g. 10 slides, perPage 3 → end = 7
```

### Options (commonly used)

| Option | Values | Description |
|---|---|---|
| `type` | `'slide'`, `'loop'`, `'fade'` | Carousel behaviour |
| `perPage` | number | Slides visible at once |
| `perMove` | number | Slides to move per interaction |
| `gap` | CSS value | Gap between slides |
| `padding` | CSS value or object | Padding around the track |
| `autoplay` | boolean | Enable autoplay |
| `interval` | ms | Autoplay interval |
| `speed` | ms | Transition speed |
| `arrows` | boolean | Show/hide native arrows |
| `pagination` | boolean | Show/hide native dots |
| `drag` | boolean | Enable drag/swipe |
| `breakpoints` | object | Responsive option overrides |
| `noDrag` | selector | Elements excluded from drag |

**Readonly options** — `type`, `drag`, `direction` etc. cannot be changed after `mount()`. Only responsive options (`perPage`, `gap`, `padding`, etc.) can be updated via `mySlider.options = { ... }`.

---

## 9. RESIZE HANDLING

Use debounced resize to refresh options:

```js
const debounce = (fn, threshold) => {
  let timeout;
  threshold = threshold || 200;
  return function debounced(...args) {
    clearTimeout(timeout);
    const context = this;
    timeout = setTimeout(() => {
      fn.apply(context, args);
    }, threshold);
  };
};

window.addEventListener('resize', debounce(() => {
  mySlider.options = mySlider.options;  // Forces re-evaluation
  updateButtonStates();
}, 300));
```

The `mySlider.options = mySlider.options` pattern forces Splide to re-evaluate responsive breakpoints without destroying/remounting.

---

## 10. BRICKS CODE ELEMENT FORMAT

The integration script lives in a Bricks `code` element, typically at root level (`parent: 0`), as a sibling to the sections it controls.

```json
{
  "id": "xxxxxx",
  "name": "code",
  "parent": 0,
  "children": [],
  "settings": {
    "code": "<script>...</script>",
    "executeCode": true,
    "signature": "",
    "user_id": 7,
    "time": 0,
    "noRoot": true
  },
  "label": "Slider Integration JS",
  "themeStyles": []
}
```

| Setting | Value | Purpose |
|---|---|---|
| `executeCode` | `true` | Runs on frontend |
| `noRoot` | `true` | No wrapper `<div>` rendered |
| `signature` | `""` | Auto-regenerates on first save |
| `user_id` | integer | Author ID, auto-set on save |
| `time` | integer | Timestamp, auto-set on save |
| `themeStyles` | `[]` | Required empty array |

---

## 11. MULTI-SLIDER SETUP

The `bt-carousel-component2` script supports multiple sliders on one page. Each slider is registered in the `sliderClasses` array:

```js
const sliderClasses = ['bt-carousel-component2'];
```

To add a new slider type, add its class to the array:
```js
const sliderClasses = ['bt-carousel-component2', 'bt-carousel-testimonials'];
```

The script loops through all matching elements and initialises each independently. Controls, progress bars, and fractions are linked per-slider via the `bt-data-slider` attribute.

---

## 12. AJAX / POPUP SUPPORT

For sliders inside AJAX-loaded content (e.g. Bricks popups), reinitialise after content loads:

```js
document.addEventListener('bricks/ajax/popup/loaded', (event) => {
  const popupId = event.detail.popupId;
  if (popupId == 1867) {  // Target specific popup ID
    setTimeout(initializeSliders, 250);
  }
});
```

The 250ms delay ensures Bricks has fully initialised the Splide instance inside the popup.

---

## 13. BRICKS SLIDER PANEL SETTINGS

When using the native nestable slider, set these in the Bricks panel:

| Setting | Recommendation |
|---|---|
| Arrows | **OFF** if using external `bt-slidernav-prev`/`bt-slidernav-next` |
| Pagination | **OFF** if using custom progress bar or fractions |
| Autoplay | Set in panel; script reads `mySlider.options.interval` |
| Type | `slide` (default), `loop`, or `fade` |
| Per Page | Set per breakpoint in panel; script reads `mySlider.options.perPage` |

The script reads options from the Splide instance at runtime — no need to duplicate values in JS.

---

## 14. DOM STRUCTURE (RENDERED)

Bricks nestable slider renders this DOM:

```html
<div id="brxe-{elementId}" class="brxe-slider-nested bt-carousel-component2 splide">
  <div class="splide__track">
    <ul class="splide__list">
      <li class="splide__slide brxe-block">
        <!-- Slide content (nestable — any Bricks elements) -->
      </li>
      <li class="splide__slide brxe-block">
        <!-- Slide content -->
      </li>
    </ul>
  </div>
  <!-- Native arrows (if enabled): -->
  <div class="splide__arrows">
    <button class="splide__arrow splide__arrow--prev">...</button>
    <button class="splide__arrow splide__arrow--next">...</button>
  </div>
  <!-- Native pagination (if enabled): -->
  <ul class="splide__pagination">
    <li><button class="splide__pagination__page is-active">...</button></li>
  </ul>
</div>
```

Each slide is a `block` element (nestable) — you can place any Bricks elements inside.

---

## 15. COMPLETE bt-carousel-component2 REFERENCE

The full integration script. Add new slider classes to `sliderClasses` array. Each slider needs `bt-data-slider` attribute plus matching attributes on its external controls.

```js
document.addEventListener('DOMContentLoaded', () => {
  const initializeSliders = () => {
    const sliderClasses = ['bt-carousel-component2'];
    const sliderSelector = sliderClasses.map(cls => `.${cls}`).join(', ');

    const debounce = (fn, threshold) => {
      let timeout;
      threshold = threshold || 200;
      return function debounced(...args) {
        clearTimeout(timeout);
        const context = this;
        timeout = setTimeout(() => fn.apply(context, args), threshold);
      };
    };

    const initSlider = (sliderElement) => {
      const sliderId = sliderElement.id;
      const allInstances = window.bricksData?.splideInstances;
      let mySlider = false;

      // Find instance by ID or DOM element
      for (const key in allInstances) {
        if (key === sliderId) { mySlider = allInstances[key]; break; }
      }
      if (!mySlider) {
        for (const key in allInstances) {
          if (allInstances[key].root === sliderElement) {
            mySlider = allInstances[key]; break;
          }
        }
      }

      if (!mySlider) return;

      const dataSlider = sliderElement.getAttribute('bt-data-slider');

      // ── Progress bar ──
      const progressBar = document.querySelector(
        `.bt-slider-progressbar[bt-data-slider="${dataSlider}"]`
      );
      if (progressBar) {
        const progressType = progressBar.getAttribute('bt-data-progress-type');
        if (progressType === 'progress') {
          const update = () => {
            const end = mySlider.Components.Controller.getEnd() + 1;
            const rate = Math.min((mySlider.index + 1) / end, 1);
            progressBar.style.width = String(100 * rate) + '%';
          };
          mySlider.on('mounted move', update);
          update();
        } else if (progressType === 'animated') {
          const interval = mySlider.options.interval || 3000;
          const start = () => {
            progressBar.style.transitionDuration = interval + 'ms';
            progressBar.style.width = '100%';
          };
          const reset = () => {
            progressBar.style.transitionDuration = '0ms';
            progressBar.style.width = '0%';
            setTimeout(start, 10);
          };
          mySlider.on('autoplay:playing', start);
          mySlider.on('move', reset);
          start();
        }
      }

      // ── External nav buttons ──
      const myPrevBtn = document.querySelector(
        `.bt-slidernav-prev[bt-data-slider="${dataSlider}"]`
      );
      const myNextBtn = document.querySelector(
        `.bt-slidernav-next[bt-data-slider="${dataSlider}"]`
      );

      const updateButtonStates = () => {
        const totalSlides = mySlider.length;
        const slidesPerPage = mySlider.options.perPage || 1;
        const lastSlideIndex = totalSlides - slidesPerPage;
        if (mySlider.options.type === 'loop') {
          if (myPrevBtn) myPrevBtn.disabled = false;
          if (myNextBtn) myNextBtn.disabled = false;
        } else {
          if (myPrevBtn) myPrevBtn.disabled = mySlider.index === 0;
          if (myNextBtn) myNextBtn.disabled = mySlider.index >= lastSlideIndex;
        }
      };

      if (myPrevBtn && myNextBtn) {
        myPrevBtn.addEventListener('click', () => mySlider.go('-1'));
        myNextBtn.addEventListener('click', () => mySlider.go('+1'));
        mySlider.on('mounted moved', updateButtonStates);
        updateButtonStates();
      }

      // ── Fractions ──
      const fractionsEl = document.querySelector(
        `[bt-data-slider="${dataSlider}"][bt-data-fractions="true"]`
      );
      if (fractionsEl) {
        const update = () => {
          fractionsEl.innerText = `${mySlider.index + 1}/${mySlider.length}`;
        };
        mySlider.on('mounted move', update);
        update();
      }

      // ── Resize ──
      window.addEventListener('resize', debounce(() => {
        mySlider.options = mySlider.options;
        updateButtonStates();
      }, 300));
    };

    // Init all sliders
    document.querySelectorAll(sliderSelector).forEach(initSlider);

    // ── Sync sliders ──
    const mainSliders = document.querySelectorAll('[bt-data-sync="main"]');
    mainSliders.forEach(mainSlider => {
      const syncValue = mainSlider.getAttribute('bt-data-slider');
      const thumbSliders = document.querySelectorAll(
        `[bt-data-sync="${syncValue}"]`
      );
      thumbSliders.forEach(thumbSlider => {
        const mainId = mainSlider.dataset.scriptId;
        const thumbId = thumbSlider.dataset.scriptId;
        setTimeout(() => {
          const main = bricksData.splideInstances[mainId];
          const thumb = bricksData.splideInstances[thumbId];
          main.sync(thumb);
        }, 0);
        window.addEventListener('resize', debounce(() => {
          const main = bricksData.splideInstances[mainId];
          const thumb = bricksData.splideInstances[thumbId];
          main.sync(thumb);
        }, 300));
      });
    });
  };

  // Initial call
  setTimeout(initializeSliders, 250);

  // Uncomment for AJAX popup support:
  // document.addEventListener('bricks/ajax/popup/loaded', (event) => {
  //   if (event.detail.popupId == 1867) {
  //     setTimeout(initializeSliders, 250);
  //   }
  // });
});
```

---

*End of SLIDERS.md*
