# OpenCode Emergency Handoff Checkpoint (`HANDOFF.md`)

**Date:** 2026-09-21  
**Repository:** `Th3Tamas/store-theme` on branch `main`  
**Target Store:** `https://sonarlineaudio.space` (Payhip: `https://payhip.com/sonarlineaudio`)  
**Baseline Theme:** Payhip "Tusk" Theme Engine  
**Aesthetic Reference:** Atrum Lab brutalist wireframe system (`#0d0d0d` / `#0a0a0a` / `#121212` backgrounds, 1px borders `rgba(255, 255, 255, 0.08)`, `Space Mono` / `Space Grotesk` / `Inter`, crimson red `#FF3B30` / `#FF2A2A` accents).

---

## 1. Executive Summary & Tech Stack

- **Platform:** Payhip Custom Theme Injection. Payhip enforces a server-side template structure (Tusk theme) with rigid DOM wrappers (`.section-collection`, `.collection-products`, `.grid-list`, `.card-wrapper`).
- **Delivery Pipeline:**
  - Files are maintained in git repository `Th3Tamas/store-theme`.
  - Injected into Payhip store via jsDelivr CDN URLs:
    - CSS: `https://cdn.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-store-pages-custom.css`
    - JS/HTML Header Injection: `https://cdn.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-header-html-js-code-injection.html`
  - Any commit to `main` must immediately be followed by a CDN cache purge via jsDelivr API.
- **Visual Design Spec:**
  - Dark brutalist wireframe panels (`#0f0f0f` card backgrounds, `1px solid rgba(255, 255, 255, 0.08)` borders, `8px` border radius).
  - Continuous infinite auto-looping marquee rail drifting right-to-left via hardware-accelerated CSS keyframes (`@keyframes sonarlineInfiniteRail { 0% { transform: translate3d(0, 0, 0); } 100% { transform: translate3d(-50%, 0, 0); } }`).
  - Zero chevrons (`<` and `>` buttons permanently deleted from DOM and CSS).
  - Hover pause: marquee drift halts immediately on hover over any card or the track.
  - Zero sideways wheel scroll: vertical mouse wheel scrolling over cards scrolls the page vertically, never horizontally.
  - Product price tag: naked white Space Mono text (`font-size: 0.88rem`, `font-weight: 700`) pinned strictly to the bottom-right corner of each card with ZERO concentric background ripple or drop shadow.
  - Single glowing red dot + crimson title (`MOST POPULAR`) in section header strip (`#121212`) above the full-width `#0a0a0a` collection body.
  - Dynamic highlight shortcode (`[[highlighted: true]]`): applies an electric crimson border (`1px solid #FF3B30 !important; box-shadow: 0 0 12px rgba(255, 59, 48, 0.3) !important;`) that strictly persists across Set 1 and Set 2 clone sets during infinite looping.

---

## 2. File Structure Map

```
store-theme/
├── storewide-store-pages-custom.css        # Global CSS injected into Payhip's Custom CSS field
├── storewide-header-html-js-code-injection.html # Main runtime engine injected into Payhip Header Code
├── storewide-footer-html-js-code-injection.html # Secondary footer script injection
├── all-collection-pages-embed.html         # Page-specific embed template
├── all-custom-pages-embed.html             # Custom page embed template
├── all-product-pages-embed.html            # Product page embed template
├── FEATURES.md                             # Master feature tracker & changelog (Releases 1–17)
└── HANDOFF.md                              # This file (OpenCode handoff reference)
```

### File Responsibilities

1. **`storewide-store-pages-custom.css`**:
   - Primary stylesheet injected into Payhip Custom CSS.
   - Handles full-width section breakout (`100vw`, `left: 50%`, `margin-left: -50vw`).
   - Two-tone gray background palette (`#0a0a0a` body, `#121212` header strip).
   - High-specificity container overrides for `.sl-carousel-viewport`, `.sl-carousel-track`, `.card-wrapper`, and `.product-card`.
   - Card dimension locks (`flex: 0 0 280px !important; width: 280px !important;`).
   - Price tag clean resets (`background: transparent !important; box-shadow: none !important; -moz-box-shadow: none !important;`).
   - Mobile media queries (`@media (max-width: 768px)`, `@media (max-width: 640px)`, `@media (max-width: 480px)`).

2. **`storewide-header-html-js-code-injection.html`**:
   - Main JavaScript and immediate critical style injection engine.
   - `CAROUSEL_CSS` constant: mirrors critical carousel rules so they apply before external stylesheets finish downloading.
   - `injectImmediateStyles()`: runtime style block injected at `document_start`.
   - `buildCarousel(grid)`:
     - Discovers collection cards and filters top-level cards.
     - Unwraps intermediate `.grid-list` or row wrappers so cards are direct children of `.sl-carousel-track`.
     - Shortcode parsing: evaluates `[[highlighted: true]]` and `[[tag: ...]]` on cards BEFORE cloning.
     - Dual-track cloning: duplicates cards if `< 6`, then mirrors Set 1 into Set 2 (`data-sl-clone="true"`).
     - Cloned cards strictly inherit `.sonarline-card--highlighted`, `data-highlighted="true"`, and `data-sl-highlighted="true"`.
     - Injects section header: dynamic title (`MOST POPULAR`) with single glowing red dot and unique product counter (`originalCards.length + ' PRODUCTS'`).
     - Manual drag scrub with seamless linear drift resumption (calculates normalized offset ratio and sets `animation-delay`).
     - Hover pause listeners (`mouseover`/`mouseout`, `mouseenter`/`mouseleave`) toggling `.is-hovered`.
   - `cleanProductCards()`: runs two-row metadata builder (`.sonarline-card-metadata`, `.sonarline-card-row-top`, `.sonarline-card-row-bottom`), parses tags and audio, and strips tokens from visible DOM.
   - `sonarlineExtractTags(raw)`: multi-tag extractor supporting multiple delimiters (commas, semicolons, pipes, bullets, newlines, `//`), preserving `/` in compound tags (e.g. `BUNDLE / KITS`), and stripping HTML formatting.
   - `sonarlineSyncCardShortcodes()`: background fetcher parsing shortcodes from `/b/...` and `/p/...` product pages and caching in `sessionStorage`.
   - `sonarlineInjectProductPageTags()`: sitewide tag injection on product detail pages.

3. **`storewide-footer-html-js-code-injection.html`**:
   - Secondary deferred script injection; currently houses minimal auxiliary hooks.

4. **`FEATURES.md`**:
   - Master changelog through Release 17.

---

## 3. Current Status, Regressions Addressed & Active Verification

### A. Firefox Multi-Row Grid Collapse on Collection Rail
- **Status:** Addressed in Release 16 / Release 17; needs live visual verification in Firefox Gecko.
- **The Defect:** Payhip's native theme styles (`.collection-products`, `.grid`, `.theme-tusk` grid classes) apply `display: grid` with multiple columns or `flex-wrap: wrap`, causing cards in Firefox to break into a 4-column multi-row grid instead of a single infinite horizontal row.
- **The Solution Implemented:**
  1. High-specificity card selectors in CSS lines 1016–1046 (`#page-section-collection .card-wrapper`, `div.card-wrapper.product-card-wrapper`, etc.) were scoped with `:not(.sl-carousel-track *):not([data-sl-rail="done"] *)`, preventing `width: 100% !important` and `flex: 1 1 auto !important` leaks into the carousel.
  2. In `buildCarousel()`, an unwrapping loop lifts any card wrapped inside an intermediate container up to be a direct child of `.sl-carousel-track`.
  3. Added `flex-shrink: 0 !important;` to `.sl-carousel-track`.
  4. Direct inline styles on each card: `card.style.setProperty('flex', '0 0 280px', 'important'); card.style.setProperty('width', '280px', 'important');`.
- **OpenCode Action Item:** Open `https://sonarlineaudio.space` in desktop Firefox and verify that all cards sit on a single continuous horizontal line with 0 row wrapping.

### B. Firefox Nested Price-Tag Opacity Ripple (`FROM €29.99`)
- **Status:** Addressed in Release 17.
- **The Defect:** In Firefox, child price elements (`.price`, `.price-item`, `.sonarline-card-price-wrap`) had multiple compounding background fills, borders, and shadows that created a concentric contour map effect. Furthermore, the price tag was floating or left-aligned.
- **The Solution Implemented:**
  1. All nested price elements (`.sonarline-card-price-wrap *`, `.sonarline-card-row-bottom .price`, `.sonarline-card-row-bottom [class*="price"]`) have `background: transparent !important; box-shadow: none !important; -moz-box-shadow: none !important; border: none !important; filter: none !important;`.
  2. Price container pinned to bottom right:
     ```css
     .sonarline-card-price-wrap {
       margin-top: auto !important;
       margin-left: auto !important;
       text-align: right !important;
       align-self: flex-end !important;
       display: inline-flex !important;
       align-items: flex-end !important;
       justify-content: flex-end !important;
       color: #ffffff !important;
       font-family: 'Space Mono', monospace !important;
       font-size: 0.88rem !important;
       font-weight: 700 !important;
       letter-spacing: 0.05em !important;
     }
     ```
  3. Leftover duplicate price elements outside `sonarline-card-metadata` are cleaned up.
- **OpenCode Action Item:** Confirm in Firefox that the price tag renders as naked white Space Mono text at the bottom-right of the card with zero contour ripple.

### C. `[[highlighted: true]]` Detection & Preservation on Clones
- **Status:** Addressed in Release 16 / Release 17.
- **The Defect:** Highlight was dropping on cloned cards during the loop, failing on Firefox, or getting wiped out on hover.
- **The Solution Implemented:**
  1. Card iteration uses non-singleton `.forEach()` (no early return/break).
  2. Regex matches both `.textContent` and `.innerHTML` with `/\[\[highlighted:\s*true\s*\]\]/i` (supports optional trailing whitespace before `]]`).
  3. Shortcode is evaluated and `.sonarline-card--highlighted` is added BEFORE cloning.
  4. When cards are cloned into Set 2, classes and data attributes (`data-highlighted="true"`, `data-sl-highlighted="true"`) are explicitly copied.
  5. Hover specificity override: `.card-wrapper.sonarline-card--highlighted:hover { border-color: #FF3B30 !important; }`.
- **OpenCode Action Item:** Confirm that cards tagged with `[[highlighted: true]]` (`SILVER` and `STAY BACK`) retain their `#FF3B30` red border continuously as the rail loops.

### D. Sitewide Multiple Tag System
- **Status:** Implemented in Release 17.
- **The Defect:** Previously only 1 tag showed, and only on collection cards.
- **The Solution Implemented:**
  1. `sonarlineExtractTags()` extracts multiple tags separated by commas, semicolons, pipes, bullets, newlines, or `//`.
  2. Supports `[[tag: A, B]]`, `[[tags: A, B]]`, `[tag: A, B]`, and multiple separate tokens `[[tag: A]] [[tag: B]]`.
  3. Renders `.sonarline-card-tags-wrap` with multiple `.sonarline-card-tag-pill` badges on cards.
  4. Injects `.sonarline-product-tags-wrap` with `.sonarline-product-tag-pill` elements on product detail pages (`/b/...`, `/p/...`, `/product/...`, `/item/...`).
  5. Injects `.sonarline-modal-tags-wrap` inside the product preview modal.
- **OpenCode Action Item:** Test assigning `[[tag: DRUM KIT, ONE-SHOT]]` or `[[tag: DRUM KIT]] [[tag: ONE-SHOT]]` to a product and verify that two distinct pills appear on both the card and the product detail page.

---

## 4. Critical DOM Rules & Selectors (DO NOT REMOVE)

The following selectors and elements are structurally required by the theme and must **never** be deleted or wiped:

1. **Root Flow Containers:**
   - `.content-main-wrapper`, `#page-wrap`: Payhip's main document wrappers. Must retain `overflow-x: clip !important; overflow-y: visible !important;` and dynamic `padding-top: var(--sonarline-header-height)`.
2. **Fixed Navbar:**
   - `header#header`: Native Payhip navigation bar. Must remain `z-index: 50 !important; position: fixed !important; top: 0 !important;`.
3. **Hero Section:**
   - `.section-hero`, `[data-section-type="hero"]`, `#content-section-LBNmnP42zK`: Contains the procedural blueprint grid canvas and centered headline. Mountain image must remain suppressed (`display: none !important;`).
4. **Collection Section & Carousel Elements:**
   - `#page-section-collection`, `[data-section-key="collection"]`: Must keep `100vw` breakout styling.
   - `.sl-carousel-viewport`: The masking container with edge fade gradients. Must keep `overflow: hidden !important; width: 100% !important;`.
   - `.sl-carousel-track`: The animated marquee track. Must keep `display: flex !important; flex-wrap: nowrap !important; width: max-content !important; flex-shrink: 0 !important; animation: sonarlineInfiniteRail 35s linear infinite !important;`.
   - `.card-wrapper`, `.product-card`: Must retain `flex: 0 0 280px !important; width: 280px !important; min-width: 280px !important; max-width: 280px !important;`.
5. **Card Metadata Hierarchy:**
   - `.sonarline-card-metadata`: Two-row flex column container.
   - `.sonarline-card-row-top`: Upper row holding title (`h2`, `h3`, `.product-title`).
   - `.sonarline-card-row-bottom`: Lower row holding `.sonarline-card-tags-wrap` (left) and `.sonarline-card-price-wrap` (right).

---

## 5. Deployment Protocol & CDN Purge

When making any changes:

1. **Commit Convention:**
   ```bash
   git add <modified-files>
   git commit -m "fix(scope): clear description of change"
   git push origin main
   ```

2. **Purge jsDelivr CDN Caches (MANDATORY):**
   Execute these exact curl commands in terminal to ensure live visitors receive updated assets immediately:
   ```bash
   curl.exe -s "https://purge.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-store-pages-custom.css"
   curl.exe -s "https://purge.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-header-html-js-code-injection.html"
   ```
   Both endpoints will return `{"status": "finished"}` on success.

3. **Verify via Commit-Specific URL:**
   If jsDelivr `@main` cache lags, verify immediately via commit SHA:
   `https://cdn.jsdelivr.net/gh/Th3Tamas/store-theme@<COMMIT_SHA>/storewide-store-pages-custom.css`

---

## 6. Quick Reference: Shortcodes Supported

| Shortcode | Example | Behavior |
|---|---|---|
| `[[highlighted: true]]` | `SILVER [[highlighted: true]]` | Applies crimson red border (`#FF3B30`) + glow. Stripped from visible text. |
| `[[highlighted: false]]` | `BEAT PACK [[highlighted: false]]` | Explicitly keeps standard border. Stripped from visible text. |
| `[[tag: ...]]` | `[[tag: DRUM KIT, SAMPLES]]` | Renders individual pills on cards, modal, and product detail pages. |
| `[[audio: URL]]` | `[[audio: https://.../demo.mp3]]` | Binds audio URL for Backblaze HTML5 preview player in modal. |
| `[[preloader: ...]]` | `[[preloader: BOOT / INIT]]` | Customizes boot lines in the HUD terminal preloader. |
| `[[loader-title: A / B]]` | `[[loader-title: SONARLINE / LAB]]` | Customizes brand header in preloader with crimson red accent. |
| `[[loader-secondary: T]]` | `[[loader-secondary: SOUND LAB]]` | Customizes subtext in preloader. |
| `[[newsletter-open: true]]` | `GET EXCLUSIVE DEALS [[newsletter-open: true]]` | Footer card status pill shows green OPEN; form enabled. Stripped from visible text. |
| `[[newsletter-open: false]]` | `[[newsletter-open: false]]` | Footer card status pill shows red CLOSED; form dimmed and disabled. Stripped from visible text. |
