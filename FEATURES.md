# Sonarline Store Theme — Feature Tracker & Changelog

**Project:** Sonarline Audio Storefront (`https://sonarlineaudio.space`)  
**Platform:** Payhip Custom Theme Injection  
**Repository:** `Th3Tamas/store-theme`  
**Last Updated:** 2026-09-21  

---

## Status Legend
- `[x]` **Completed & Verified** — Deployed, verified in live DOM, and active in production.
- `[/]` **In Progress** — Diagnosed or currently under active development.
- `[!]` **Known Issue / Defect** — Immediate bug requiring remediation.
- `[ ]` **Planned / Roadmap** — Future feature or enhancement.

---

## Table of Contents
1. [Core Architecture & Visual Identity](#1-core-architecture--visual-identity)
2. [Preloader & HUD Terminal Sequence](#2-preloader--hud-terminal-sequence)
3. [Hero & Headline Section](#3-hero--headline-section)
4. [Announcement Marquee Ticker](#4-announcement-marquee-ticker)
5. [Product Cards](#5-product-cards)
6. [Product Preview Modal & Audio Engine](#6-product-preview-modal--audio-engine)
7. [CMS Shortcode Parser Engine](#7-cms-shortcode-parser-engine)
8. [Known Issues & Immediate Fixes](#8-known-issues--immediate-fixes)
9. [Future Roadmap & Enhancements](#9-future-roadmap--enhancements)
10. [Changelog & Revision History](#10-changelog--revision-history)

---

## 1. Core Architecture & Visual Identity

- `[x]` **Brutalist Dark Wireframe Aesthetic:**
  - High-contrast dark background palette (`#0d0d0d`, `#111111`, `#161616`).
  - Subtle wireframe borders (`1px solid rgba(255, 255, 255, 0.12)`).
  - Sonarline brand accent color: International Orange (`#ff5500` / `#ff4400`).
- `[x]` **Typography System:**
  - `Space Grotesk` — Headlines, hero typography, modal headers, uppercase accents.
  - `Space Mono` — Technical readouts, terminal syntax, price tags, audio timestamps.
  - `Inter` — Body text, descriptions, UI labels.
- `[x]` **Custom Kinetic Cursor:**
  - Multi-element cursor with `mix-blend-mode: difference` inversion across all surfaces (rAF-coalesced input).
  - Centered 6px white dot + trailing 24px outer ring with smooth rAF lerp physics.
  - Interactive reaction: dot smoothly expands from 6px to 24px to fill the outer ring when hovering over clickable targets (`a`, `button`, cards, badges, pills, etc.).
- `[-]` **Desktop Drag-Selection Box (`#sl-selection-box`):**
  - Scrapped and removed per user direction.
- `[x]` **Layout Cleanup & Overflow Protection:**
  - Enforced `overflow-x: clip` on root containers to eliminate horizontal page scrollbars.
  - Stripped default Payhip footer subscription/newsletter form.
  - Native Payhip header action buttons preserved in stock state (Search, Account, Cart).
- `[x]` **Automated CDN Delivery Pipeline:**
  - Linked via jsDelivr CDN (`https://cdn.jsdelivr.net/gh/Th3Tamas/store-theme@main/...`).
  - Automated cache purging via jsDelivr purge API upon release.

---

## 2. Preloader & HUD Terminal Sequence

- `[x]` **Fullscreen HUD Boot Overlay (`#sonarline-hud-preloader`):**
  - Dark scanline CRT effect and blinking green/white cursor.
  - Real-time sequential terminal output line rendering with typing delays.
- `[x]` **CMS Shortcode Integration (`[[preloader: ...]]`):**
  - Admins can customize boot text directly in Payhip rich text sections.
  - Source container is automatically hidden from page layout to prevent content flash.
  - Robust fallback sequence when shortcode is omitted.
  - Prepends red prompt arrows (`>`) and formats `[OK]` status markers to the right edge with dot leaders (`......... [OK]`).
- `[x]` **Brand Header Shortcodes (`[[loader-title: ...]]` & `[[loader-secondary: ...]]`):**
  - `[[loader-title: FIRST / SECOND]]`: Primary title split by `/` with first part in bold uppercase white `Space Grotesk`, and both the slash (`/`) and second part in solid crimson red (`#FF2A2A` / `#FF3B30`). Defaults to `SONARLINE / lab`.
  - `[[loader-secondary: TEXT]]`: Centered subtext under the title in muted, tracked-out `Space Mono` (`letter-spacing: 0.25em`). Defaults to `SOUND DESIGN LAB`.
  - DOM sanitization strips all tokens from normal page flow.
- `[x]` **Dynamic Incrementing Progress Bar & Percentage Engine:**
  - Pinned horizontal hairline wireframe track (`rgba(255, 255, 255, 0.12)`) spanning the terminal container.
  - Active fill bar in `#FF2A2A` / `#FF3B30` positioned directly above the status footer row (`READY` on left, percentage counter on right).
  - Ascending pseudo-random incremental engine (`current += Math.floor(Math.random() * 20) + 10`).
  - Holds for 200ms at `100%`, then smoothly fades out and unmounts the overlay.
- `[x]` **Homepage Scoping:**
  - Preloader runs strictly on the homepage (`/`) and is suppressed on individual product pages (`/b/...`, `/p/...`) to prevent navigation friction.

---

## 3. Hero & Headline Section

- `[x]` **Technical Blueprint Grid:**
  - Procedural wireframe grid pattern replacing Payhip's default mountain hero image.
- `[x]` **Centered Headline & Categories:**
  - Centered typography for the main headline and subheadings.
  - Centered category row: `DRUM KITS • SAMPLE PACKS • MULTI-KITS • ANALOG STEMS • ONE-SHOTS`.
  - Custom monospace bullet styling (`•`) with calibrated vertical alignment.
- `[x]` **Scroll Parallax & Blur Engine:**
  - Scroll-linked opacity falloff and progressive backdrop blur on hero background elements during downward scroll.

---

## 4. Announcement Marquee Ticker

- `[x]` **Slim Wireframe Ticker:**
  - Compact ~36px height container with a single outer wireframe border.
  - Removed double borders and unwanted glow/drop-shadow effects.
- `[x]` **Strict Dot Separators & Space Mono Typography:**
  - Rendered in crisp, solid `#FF2A2A` Space Mono without glow or halos.
  - Enforced bullet separators (`•`) with balanced horizontal spacing between each phrase:
    `ANALOG WARMTH • PURE GRIT • NO FILLER SOUNDS • PREMIUM SOUNDS • ...`.
  - Calibrated vertical centering with equal top/bottom clearance.
- `[x]` **Continuous Marquee Loop:**
  - Seamless CSS/JS animation with pause-on-hover interaction.
- `[x]` **Header Offset Calibration & Static Flow (sticky reverted per user request):**
  - Ticker sits in normal document flow using `position: relative !important; top: auto !important; left: auto !important; transform: none !important; width: 100% !important; z-index: 1 !important;` — scrolls away naturally, never pinned.
  - Navbar is pinned above it (`header#header z-index: 50`), so the ticker can never paint over the navbar.
  - Header engine (`syncHeaderOffset` / `initHeaderOffsetEngine`) maintains `--sonarline-header-height` + `padding-top` on ALL `.content-main-wrapper, #page-wrap` nodes (first-match single-node bug fixed); JS never sets `sticky`/`fixed` on tickers, and ticker writes were removed from the scroll path (one-time `enforceTickerStatic()` with `data-sl-ticker-static` markers).
  - Parent containers keep `overflow-x: clip !important; overflow-y: visible !important;` as horizontal-bounce guards (safe for static flow).

---

## 5. Product Cards

- `[x]` **Wireframe Card Framing (Reference Image 1 Spec):**
  - Dark card container with subtle 1px border and rounded corners.
  - 1:1 square media container flush to top, left, and right borders.
  - Eliminated generic center-stacked fallback card layouts.
- `[x]` **Two-Row Metadata Layout:**
  - **Upper Row:**
    - Left: Product title in bold, clean uppercase monospace font.
    - Right: Circular wireframe info badge (`!`) with hover reaction.
  - **Lower Row:**
    - Left: Wireframe pill badge for category tags (e.g., `DRUM KIT`).
    - Right: Price tag aligned flush right in clean monospace type.
- `[x]` **Conditional Tag Pill Visibility:**
  - Removed arbitrary keyword inference (e.g., default `SOUND LAB`).
  - Pill badge only displays when an explicit `[[tag: ...]]` shortcode is assigned.
  - Pill element is completely hidden (`display: none !important`) if no tag exists.
- `[x]` **Mobile Card Centering:**
  - Padding override on viewports `<= 480px` for consistent card grid alignment.
- `[x]` **Collection Card Shortcode Sync:**
  - Background fetch of card product URLs (`/b/...`, `/p/...`), same-origin only, max 3 concurrent.
  - Parses `[[tag: ...]]` / `[[audio: ...]]` from product HTML, caches `sessionStorage sonarline_prod_<path>`, populates pill + `data-audio-src` modal binding.
  - `sonarlineScrubProductDescriptions()` strips raw tokens from `.product-description` on detail pages.

---

## 6. Product Preview Modal & Audio Engine

- `[x]` **Brutalist Preview Modal (`#sonarline-product-modal`):**
  - Triggered by clicking the circular `!` badge on any product card.
  - Displays product thumbnail, title, price, description, and direct purchase link.
- `[x]` **HTML5 Backblaze Audio Player:**
  - Audio streaming directly from Backblaze B2 URLs.
  - Play/Pause toggle with animated playback state.
  - Interactive timeline scrubber with elapsed time and total duration counter.
  - Dedicated volume control slider.
  - Auto-pauses and cleans up playback when modal is closed or another track is selected.

---

## 7. CMS Shortcode Parser Engine

- `[x]` **Terminal Shortcode (`{{terminal: ...}}` / `{{TERMINAL: ...}}`):**
  - Strips Payhip WYSIWYG HTML tags, entities, and non-standard dashes.
  - Semantic multi-color syntax highlighting:
    - User/Host (`root@sonarline:~$`): Green
    - Command/Path (`archive`): Cyan
    - Flags/Status (`--status active`): Orange
- `[x]` **Preloader Shortcode (`[[preloader: ...]]`):**
  - Extracts boot lines from rich text and feeds into HUD terminal.
- `[x]` **Product Tag Shortcode (`[[tag: ...]]`):**
  - Parses category tags for card pill badges and removes raw bracket delimiters from visible titles.
- `[x]` **Audio Preview Shortcode (`[[audio: ...]]`):**
  - Extracts audio preview URLs for modal playback.
- `[x]` **Inline Audio Scrubber (`{{audioplay: URL}}` / `{{audioplay: [URL]}}`):**
  - Renders a minimal play + wave-bar scrubber in place with elapsed/total readout and click-to-seek; instances pause each other.
- `[x]` **Chained & Inline Tags (`[[tag:A]] [[tag:B]]`, `<span class="sl-tag">`):**
  - Multi-tag chaining collects every token into individual schematic pills; inline `[[tag: ...]]` in text blocks renders dual-classed (`.sonarline-tag-pill.sl-tag`) pills.

---

## 8. Known Issues & Immediate Fixes

### Issue 1: Ticker Buried Under Fixed Navbar
- **Status:** `[x]` **Resolved**
- **Description:** Payhip's fixed navbar (`header#header`, ~132px high) sits at `y = 0px`. Because `.content-main-wrapper` lacked top padding, the 36px marquee ticker was obscured beneath the navbar.
- **Resolution:**
  1. Defined `:root { --sonarline-header-height: 132px; }`.
  2. Implemented `syncHeaderOffset()` to measure navbar height on load, resize, and scroll.
  3. Applied `padding-top: var(--sonarline-header-height, 132px) !important;` to `.content-main-wrapper`.

### Issue 2: Homepage Cards Do Not Inherit Product Page Shortcodes
- **Status:** `[x]` **Implemented (needs live verification)**
- **Description:** Payhip collection cards on the homepage only render product title, image, and price—product descriptions are omitted from collection card HTML. Shortcodes added to product descriptions in Payhip CMS (e.g. on `/b/Ai9Re`) are not visible to homepage cards.
- **Implementation:**
  1. `cleanProductCards()` calls `sonarlineSyncCardShortcodes(card)` when tag/audio missing.
  2. Parses `[[tag: ...]]` and `[[audio: ...]]` from fetched HTML via `sonarlineParseShortcodes()`.
  3. Caches results in `sessionStorage` (`sonarline_prod_<path>`, max 3 concurrent, same-origin only).
  4. Dynamically populates the card tag pill and `data-audio-src` modal binding via `sonarlineApplySyncedShortcodes()`.

### Issue 3: Raw Shortcode Tokens on Product Pages
- **Status:** `[x]` **Implemented (needs live verification)**
- **Description:** On individual product detail pages (`/b/...`, `/p/...`), raw shortcodes like `<p>[[audio: ...]]</p>` and `<p>[[tag: ...]]</p>` may appear in `.product-description` in plain text.
- **Implementation:**
  1. `sonarlineScrubProductDescriptions()` walks `.product-description` text nodes and strips `[[tag: ...]]` / `[[audio: ...]]` on every `cleanProductCards()` pass.

### Issue 4: Owner-Logged-In Session Loads Payhip Editor Stack
- **Status:** `[x]` **Diagnosed (shoppers unaffected)**
- **Description:** When viewed while logged in as the store owner, Payhip serves its seller/editor runtime (`editor2-shop-core.bundle.js`, ~514KB) plus extra third-party loops on top of the storefront. Profiling showed metronomic main-thread stalls with ~70% `Incremental CC` samples in that session only; logged-out sessions (what shoppers get) profile clean, as do other Payhip stores. Owner sessions may also miss card highlights/tags because background product-page fetches return editor-chrome markup.
- **Implementation:**
  1. Always judge storefront performance in a logged-out/private window.
  2. Empty shortcode sync results are never cached, so editor-chrome responses can't go stale.
  3. A 15s janitor re-processes only never-decorated cards and re-arms shortcode parsing only when raw tokens exist (silent at steady state).

---

## 9. Future Roadmap & Enhancements

- `[ ]` **Product Detail Page Brutalist Theme:**
  - Extend custom dark wireframe styling, Space Grotesk typography, and embedded Backblaze audio preview player directly to `/b/...` and `/p/...` product pages.
- `[x]` **Audio Waveform Visualizer:**
  - Interactive `<canvas>` oscilloscope and dynamic frequency bars inside the preview modal with seek-on-click support.
- `[x]` **Product Category Filter Bar:**
  - Real-time tag filtering HUD (`ARCHIVE // [ ALL ] [ DRUM KIT ] ...`) above the collection grid with zero page reload.
- `[x]` **Cart Drawer & Checkout Styling Alignment:**
  - Style Payhip's native slide-out cart drawer with matching wireframe borders, dark background, and monospace font hierarchy.
- `[x]` **Audio Volume Persistence:**
  - Save user volume preferences in `localStorage` (`sonarline_audio_vol`) across page visits.
- `[x]` **Keyboard Accessibility for Audio Player:**
  - Full keyboard shortcuts: `Space` (play/pause), `ArrowLeft`/`ArrowRight` (seek +/- 5s), `ArrowUp`/`ArrowDown` (volume +/- 10%), `Escape` (close modal).
- `[x]` **Global Sticky Bottom Audio Deck:**
  - Persistent playback when modal is closed or during page navigation with synchronized controls, scrubber, volume slider, and modal expand trigger.
- `[x]` **Card Quick-Listen Trigger:** Instant 1-click play/pause overlay on card artwork hover with animated soundwave indicator.
- `[ ]` **Audio Stem / Layer Selector in Preview Modal:** Multi-track demo auditioning via `[[audio:stem:url]]`.
- `[ ]` **Multi-Sample Directory Tree:** `[[contents: ...]]` formatted into an ASCII tree breakdown inside modals.
- `[ ]` **Instant In-Page Search & Live Card Filter HUD:** Real-time counter readouts and instant filtering.
- `[x]` **3D Hardware-Accelerated Perspective Tilt on Card Hover:**
  - Subtle perspective mouse tracking with dynamic specular glare highlight effect on collection cards.
- `[x]` **Monospace Text Scramble / Character Decryption Effect on Hover:** Cyberpunk text reveal animation on card title hover.
- `[ ]` **Dynamic Web Audio API Oscilloscope / Reactive Scanline Pulse:** Real-time visualizer canvas.
- `[ ]` **Brutalist Terms of Service & Sample Clearance Drawer:** Dedicated slide-over modal for licensing.
- `[ ]` **Floating HUD Cart Counter & Order Total Pill:** Minimalist sticky indicator for cart status.

---

## 10. Changelog & Revision History

### [2026-09-22 - Release 21]
- **Placeholder Purge**: unpopulated Payhip showcase blocks (`[data-section-key="product"]`, placeholder-SVG/example-alt details wrappers) hidden; hero-to-collections gap closed (margin 0, 24px bottom pad).
- **Symmetrical Hero HUD**: `.sl-hud-framed` hairline frame with red TL/BR corner marks replaces the one-sided schematic box on the hero band; headline stays centered.
- **Instrument-Chassis Cards**: rail cards recessed (`#0f0f11`, 0.1 hairline, 10px inset, inset bezel ring) with bordered `#050505` media beds; highlight hairline/glow, padding, and barcode alignment preserved.
- **Technical Schematic Placard Cards**: cards rebuilt as spec-sheet placards (`#111113` surface, `rgba(255,255,255,0.12)` hairline, 12px exhibition frame, corner ticks, 2px geometry); removed the white `BUY NOW`/`LISTEN` button row and its delegation; Row 1 = Space Grotesk 0.85rem title + `#01` catalog index, Row 2 = spec-strip tag pills, Row 3 = CSS barcode + big mono price bar.
- **Blueprint Section Framing**: `.sl-schematic-box` HUD corners (TL/BR accents) applied to carousel bodies and the hero band.
- **Ghost Watermark**: `ARCHIVE // COLLECTIONS` ultra-bold ghost type behind the rail with the track elevated above it.
- **Preserved**: infinite marquee drift, single row, card-only hover pause, collapsible header, highlight/clone engine, dark palette, difference cursor.
- **Kilo-Hertz Schematic Layer**: pure-CSS corner wireframe brackets on cards (gradient layers, no DOM churn), stable `SL-001` serial micro-labels, schematic action row per card (`BUY NOW` span + delegation so clones inherit behavior with zero nested-anchor risk, `LISTEN` bridged to each card's quickplay trigger), large monospace prices on cards and in modal, sharp 2px card geometry, Roboto Mono in the type stack.
- **Viewport Schematic Overlay**: single guarded `#sl-schematic-overlay` fixed layer (subtle grid + film-grain noise + viewport corner brackets), pointer-transparent, static layers only.
- **`{{audioplay: URL}}` Token**: inline minimal scrubber (play toggle, progress fill, time readout, click-to-seek) with mutual pausing between instances.
- **Deliberately unchanged**: marquee rail engine (no scroll-snap conversion), dark palette (no light inversion), no `color-dodge`/`screen` blends (measured jank risk on owner hardware), no `.section-main` surgery (page container, unsafe to bleed).

### [2026-09-22 - Release 18]
- **Sonarline Footer + `[[newsletter-open: true/false]]`**: rebuilt the native footer section around Payhip's own newsletter form (moved, never cloned, so submissions keep working) into a centered Atrum-style layout: brand mark, rounded newsletter card (`Get exclusive deals` + OPEN status pill + subtext + email + white JOIN button), mono nav row with `·` separators, and copyright. Token parsed case-insensitively from footer text and scrubbed from display; `true`/absent shows green OPEN with enabled form, explicit `false` shows red CLOSED with dimmed disabled form. Idempotent engine with `slFooterDebug()` / `slFooterRebuild()` console hooks.

### [2026-09-21 - Release 10]
- **Horizontal Carousel Conversion**: Transformed the static collection grid into an interactive, brutalist horizontal slider/carousel matching the Atrum Lab reference design. Converted track to a single-row flex container (`overflow-x: auto; scroll-snap-type: x mandatory; gap: 16px; padding: 24px 48px;`) with WebKit scrollbars hidden.
- **Fixed Card Widths & Edge Peek**: Constrained collection cards to `flex: 0 0 280px !important; min-width: 280px !important; max-width: 280px !important; scroll-snap-align: start !important;` ensuring adjacent cards peek gracefully from screen edges.
- **Dynamic Highlight Shortcode (`[[highlighted: true/false]]`)**: Removed all hardcoded active card border rules and automated center-detection (`highlightCenter`). Added dynamic CMS shortcode parser: cards containing `[[highlighted: true]]` receive `.sonarline-card--highlighted` with electric red border (`#FF3B30`) and glow (`0 0 12px rgba(255, 59, 48, 0.25)`), while cards with `false` or omitted tokens retain subtle wireframe borders (`rgba(255, 255, 255, 0.08)`). Tokens are scrubbed from visible titles, links, and descriptions.
- **Edge Fade Masks**: Applied CSS linear-gradient masks to `.sl-carousel-viewport` (`mask-image: linear-gradient(to right, transparent, black 5%, black 95%, transparent);`) for smooth card fading at viewport boundaries.
- **Brutalist Chevrons & Scroll Triggers**: Injected sleek circular `<` and `>` chevron buttons flanking the carousel track with smooth scroll increments (`grid.scrollBy({ left: direction * 300, behavior: 'smooth' })`) and boundary auto-dimming.
- **Kinetic Mouse Dragging**: Implemented mouse click-and-drag scrolling with momentum physics, kinetic deceleration friction (`velocity *= 0.92`), temporary scroll-snap suspension during drag, and accidental click suppression.
- **Wheel Scrolling Support**: Mapped vertical wheel events over the carousel track to horizontal scrolling with boundary passthrough so normal page scroll resumes once edges are reached.
- **Technical Section Header**: Injected technical header above the carousel displaying `:: MOST POPULAR` (with crimson red `::`) and dynamic real-time product counter (e.g. `X PRODUCTS`).

### [2026-09-21 - Release 9]
- **Fixed Single Product Layout**: Constrained single product cards to `max-width: 340px !important; margin: 40px auto !important;` with centered flex container, preventing single products from stretching across the full 1200px container into massive squares.
- **Removed Native Collection Header**: Hidden Payhip's native collection title and product count text (`.collection-title, .collection-products-count, [class*="collection-title"], [class*="products-count"], .collection-header, .sl-rail-header`) to eliminate "MOST POPULAR2 products" clutter.
- **Auto-Scrolling Marquee Rail**: Upgraded product rail to smoothly auto-scroll cards right-to-left continuously; added robust hover pause listeners on both viewport and cards (`mouseenter`/`mouseleave`, `pointerenter`/`pointerleave`) that halt movement immediately on hover and resume on unhover.
- **Removed 3D Perspective Tilt**: Stripped out mouse-tracking 3D perspective tilt (`init3DCardTilt`, `#sonarline-tilt-styles`, `rotateX`, `rotateY`, `perspective`).
- **Added Card Hover Lift & Stronger Border**: Elevated cards on hover with `transform: translateY(-6px) !important;` and a crisp, enhanced border `border-color: rgba(255, 255, 255, 0.6) !important;` with smooth CSS transitions.
- **Fixed Squished & Unreadable Card Tag**: Removed duplicate container padding from `.sonarline-card-metadata` (`padding: 0 !important; margin: 0 !important; width: 100% !important;`); styled `.sonarline-card-tag-pill` with `white-space: nowrap !important; font-size: 0.7rem !important; font-weight: 700 !important; color: #ffffff !important; letter-spacing: 0.08em !important; padding: 3px 8px !important; line-height: 1.2 !important; max-width: none !important; flex-shrink: 0 !important;` so tags are crisp, solid, highly readable, and never squished or truncated. Preserved child `<a>` links when stripping `[[tag:...]]` shortcodes.
- **Refined Price Tag**: Reduced font size to `0.72rem` Space Mono, eliminated nested pill backgrounds/borders across `.price-block`, `.price`, `.price__container`, etc., enforced the zero-glow mandate (no drop-shadows, glow halos, or layered blur effects), and isolated right-aligned flexbox to the `.sonarline-card-price-wrap` container so nested price text/sale items retain natural layout.
- **Removed `!` Buttons**: Completely removed product preview exclamation badges (`.sonarline-card-info-badge { display: none !important; }` and purged all instances from DOM).

### [2026-09-21 - Release 17]
- **Card Price Tag Bottom-Right Alignment**:
  - Enforced `display: flex !important; flex-direction: column !important; height: 100% !important; flex-grow: 1 !important; justify-content: space-between !important;` on cards, `.card`, `.card__content`, and `.sonarline-card-metadata`.
  - Locked `.sonarline-card-row-bottom` to `margin-top: auto !important; align-items: flex-end !important; justify-content: space-between !important; width: 100% !important;`.
  - Pinned `.sonarline-card-price-wrap` and all inner price elements to `margin-top: auto !important; margin-left: auto !important; text-align: right !important; align-self: flex-end !important; display: inline-flex !important; align-items: flex-end !important; justify-content: flex-end !important;`.
  - Purged duplicate leftover price elements outside `sonarline-card-metadata` and hid `.visually-hidden` and `.sr-only` elements inside the price wrap.
- **Reliable Card Hover Pause**:
  - Removed inline `animation-play-state: running !important;` overrides from `resumeRail()` and `endDrag()`, allowing CSS `:hover` rules to cleanly take effect.
  - Added bubbling `mouseover` and `mouseout` listeners on `grid` and `viewport` that toggle `is-hovered` and pause/resume drift when the cursor hovers over any card or the track.
  - Expanded CSS hover pause rules to cover `.sl-carousel-track:has(.card-wrapper:hover)`, `.sl-carousel-viewport:hover .sl-carousel-track`, and `.sl-carousel-body:hover .sl-carousel-track`.
- **Removed Sideways Wheel Scroll on Cards**:
  - Completely removed the vertical wheel event listener that mapped wheel delta to horizontal track scroll.
  - Changed `overflow-x: auto` to `overflow-x: visible !important;` on `[class*="product-list"]`, `[class*="products-list"]`, and related containers to prevent modern browsers from translating vertical scroll to horizontal scroll over flex containers.
  - Intercepted and blocked horizontal wheel delta and zeroed out accidental `scrollLeft` on `viewport` and `grid`.
- **Sitewide Multiple Tag System**:
  - Implemented `sonarlineExtractTags()` supporting single/double brackets (`[[tag:...]]`, `[tag:...]`, `[[tags:...]]`), multiple delimiters (comma, semicolon, pipe, bullet, newline, `//`), preserving `/` in compound tags (e.g. `BUNDLE / KITS`), and stripping rich text HTML/entities.
  - Rendered multiple tags into `.sonarline-card-tags-wrap` with individual `.sonarline-card-tag-pill` badges.
  - Extended tag rendering sitewide: dynamically injected `.sonarline-product-tags-wrap` with `.sonarline-product-tag-pill` on product detail pages (`/b/...`, `/p/...`, `/product/...`, `/item/...`), inside the product preview modal (`.sonarline-modal-tags-wrap`), and synced via `sessionStorage` across collection cards.

### [2026-09-21 - Release 16]
- **Firefox Gecko Single-Row Track Hard-Override & Unification**:
  - Scoped all 28 global card selectors in CSS lines 1016–1046 (`#page-section-collection .card-wrapper`, `div.card-wrapper.product-card-wrapper`, `.product-card-wrapper`, etc.) and mobile 480px media queries with `:not(.sl-carousel-track *):not([data-sl-rail="done"] *)`, permanently stopping `width: 100% !important` and `flex: 1 1 auto !important` leaks into carousel cards.
  - Added `flex-shrink: 0 !important;` to `.sl-carousel-track` in both stylesheets and dynamic injection to prevent Gecko flex-item shrinking.
  - Implemented script-level track unification in `buildCarousel()`: filters top-level cards (preventing nested card detachment) and unwraps any intermediate Payhip `.grid-list` or row wrappers so that all cards (original and cloned) are guaranteed direct siblings of `.sl-carousel-track`.
  - Added high-specificity dimension locks (`div.card-wrapper.product-card-wrapper`, `width: 280px !important`, `flex: 0 0 280px !important`) to ensure cards never wrap into a 4-column multi-row grid on Firefox.

### [2026-09-21 - Release 15]
- **Removed Product Image Hover Zoom**: Completely removed `transform: scale(1.03)` and animation on card hover across `storewide-store-pages-custom.css`, `CAROUSEL_CSS`, and `injectImmediateStyles()` in `storewide-header-html-js-code-injection.html`. Enforced `transform: none !important;` across all product card image selectors, ensuring product images remain completely static when hovered while the whole card lift (`translateY(-6px)`) and border enhancement remain active.

### [2026-09-21 - Release 14]
- **Hard-Override Firefox Gecko Grid Collapse**:
  - Scoped parent selectors with `:not(.sl-rail-track):not(.sl-carousel-track)` across all grid and collection containers so `#page-section-collection .grid-list` no longer clamps the track to `width: 100%`.
  - Added full specificity overrides (`#page-section-collection`, `[data-section-key="collection"]`, `.theme-tusk`, `.section-collection [class*="grid"]`) enforcing `display: flex !important; flex-direction: row !important; flex-wrap: nowrap !important; grid-template-columns: none !important; grid-auto-flow: column !important; width: max-content !important; max-width: none !important; overflow-x: visible !important;`.
  - Injected inline `!important` styles directly on `.sl-carousel-track` and `.sl-carousel-viewport` during DOM construction in `buildCarousel()`.
  - Enforced card dimension lock (`flex: 0 0 280px !important; width: 280px !important; min-width: 280px !important; max-width: 280px !important; display: flex !important; flex-direction: column !important; margin: 0 !important;`) across all high-specificity selectors to prevent Firefox from pushing cards into a 4-column multi-row grid.
  - Added `flex: 0 0 220px !important;` to the mobile `<= 640px` media query to ensure flex-basis matches width on mobile Gecko.

### [2026-09-21 - Release 13]
- **Fixed Firefox Multi-Row Grid Collapse**: Enforced strict single-row flex layout across `.section-collection .collection-products`, `.products-wrapper`, `.sl-carousel-track`, and `[data-sl-rail="done"] .sl-carousel-track` with `flex-direction: row !important; flex-wrap: nowrap !important; grid-template-columns: none !important; align-items: stretch !important; width: max-content !important; min-width: 100% !important;`. Explicitly prevented card shrinking/expansion (`flex: 0 0 280px !important; min-width: 280px !important; max-width: 280px !important; display: flex !important; flex-direction: column !important;`) across both stylesheet and dynamic style injection.
- **Fixed Price Tag Nested Opacity Ripple in Firefox**: Stripped all background fills, borders, box-shadows, -moz-box-shadows, and filters across `.sonarline-card-price-wrap *`, `.sonarline-card-row-bottom .price`, `.sonarline-card-row-bottom .price *`, `.sonarline-card-row-bottom [class*="price"]`, `.price-block`, `.price`, `.price-item`, and `.price__container`. Assigned clean `0.88rem` monospace typography (`'Space Mono', monospace`, `letter-spacing: 0.05em`) strictly to `.sonarline-card-price-wrap` leaf container.
- **Fixed `[[highlighted: true]]` Multi-Card Detection & Clone Preservation**: Replaced card iteration loops with non-singleton `.forEach()` to ensure all cards are evaluated independently without early returns/breaks. Implemented robust regex matching on both `.textContent` and `.innerHTML` (`/\[\[highlighted:\s*true\]\]/i`). Scrubbed tokens cleanly from visible text nodes without corrupting HTML. Parsed highlights on `originalCards` strictly before cloning in `buildCarousel()` and propagated `.sonarline-card--highlighted`, `data-highlighted="true"`, and `data-sl-highlighted="true"` to duplicate clone cards in Set 1 and Set 2. Updated highlight border style to `border: 1px solid #FF3B30 !important; box-shadow: 0 0 12px rgba(255, 59, 48, 0.3) !important;`.

### [2026-09-21 - Release 12]
- **Continuous Infinite Auto-Looping Rail**: Transformed the collection carousel into a continuous, seamless infinite marquee rail drifting smoothly right-to-left using hardware-accelerated CSS keyframes (`@keyframes sonarlineInfiniteRail { 0% { transform: translate3d(0, 0, 0); } 100% { transform: translate3d(-50%, 0, 0); } }`).
- **Dual-Track Cloning Architecture**: Multiplied cards if `< 6` and mirrored Set 1 into Set 2 (`data-sl-clone="true"`), ensuring a mathematically seamless `-50%` transition with 0px jump.
- **Active Class & Highlight Preservation**: Every card cloned from Set 1 into Set 2 strictly inherits and retains `.sonarline-card--highlighted`, `data-highlighted="true"`, and `data-sl-highlighted="true"`, guaranteeing that the `#FF3B30` red border never vanishes as the track loops.
- **Interactive Hover Pause**: Track smoothly pauses drift on `:hover` and `.is-hovered` (`animation-play-state: paused !important`).
- **Manual Drag Scrub with Seamless Drift Resumption**: Interactive drag and vertical wheel scrub calculate the exact current normalized offset ratio (`Math.abs(currentTranslateX) / halfWidth`) and resume the continuous CSS linear keyframe animation via `animation-delay: -${(ratio * 35).toFixed(3)}s` with zero visual jump.
- **Permanently Purged Chevrons**: Completely removed `<` and `>` chevron buttons (`.sl-carousel-chevron`, `.sl-carousel-prev`, `.sl-carousel-next`) from both DOM injection and CSS stylesheets.
- **Firefox / Gecko Price Shadow & Glow Reset**: Enforced aggressive resets targeting `.sonarline-card-price-wrap`, `.sonarline-card-row-bottom .price`, `.price-block`, `.price`, `.price__container`, `.price-item`, and pseudo-elements with `box-shadow: none !important; -moz-box-shadow: none !important; filter: none !important; outline: none !important;` to eliminate all nested stairstep drop shadows on Gecko.
- **Accurate Dynamic Header Product Counter**: Calibrated the section header product count to query only original cards (`:not([data-sl-clone])`), accurately reflecting unique products (e.g., `3 PRODUCTS`) rather than double-counting cloned cards.

### [2026-09-21 - Release 11]
- **Added:** Full-Width Section Bleed (`100vw`, `left: 50%`, `margin-left: -50vw`) with Two-Tone Gray Background (Reference Image 5): `#0a0a0a` main carousel body and `#121212` distinct upper header strip.
- **Added:** Dynamic Section Header with Single Glowing Red Dot: exactly 1 circular `#FF2A2A` dot (`box-shadow: 0 0 8px #FF2A2A`), crimson glowing title (`text-shadow: 0 0 8px rgba(255, 42, 42, 0.45)`), dynamically reading Payhip's native heading (`h2.heading-text, .js-builder-heading-text`) with fallback to "MOST POPULAR", while completely hiding native heading elements (`display: none !important`).
- **Added:** 72px Track Gutter Clearance & Elevated Chevrons: track padding updated to `32px 72px !important; overflow-x: scroll !important; cursor: grab !important;` (`grabbing` when active); brutalist chevrons (`<` and `>`) elevated to `z-index: 30 !important; pointer-events: auto !important;` at `left: 16px` and `right: 16px`, guaranteeing product artwork never overlaps or sits underneath chevrons.
- **Fixed:** Purged Text Scramble Glitch & Reset Firefox Nested Stairstep Shadows: completely removed title scramble hover listener; stripped `box-shadow`, `filter`, and background fills across `.sonarline-card-price-wrap`, `.price-block`, `.price`, `.price__container`, and `.price-item` elements.
- **Added:** Cross-Browser Multi-Card `[[highlighted: true]]` Parser: independent card processing loop setting `data-sl-highlighted="true"` and `.sonarline-card--highlighted`, allowing multiple cards to simultaneously display the `#FF3B30` brutalist border and glow.

### [2026-09-21 - Release 10]
- **Added:** 3D Hardware-Accelerated Perspective Tilt on Card Hover (`init3DCardTilt`) with dynamic specular glare highlight effect, mouse tracking, and smooth transform resets.
- **Added:** Global Sticky Bottom Audio Deck (`#sonarline-bottom-deck`) with continuous persistent playback when preview modal is closed, synchronized audio engine controls, timeline scrubber, volume slider, quick modal expand button, and close deck button.
- **Added:** Payhip Cart Drawer & Checkout Styling Alignment — styled native Payhip slide-out cart drawer with matching dark brutalist wireframe borders (`rgba(255, 255, 255, 0.12)`), `#0d0d0d`/`#111111` surfaces, Space Grotesk/Space Mono typography, and styled checkout buttons.

### [2026-09-21 - Release 7]
- **Updated Cursor:** Re-added trailing outer ring (`#sl-cursor-circle`, 32px) around a slightly smaller (10px) centered dot (`#sl-cursor-dot`), both with `mix-blend-mode: difference`. When hovering over clickable targets (`a`, `button`, cards, badges, pills, etc.), the outer ring smoothly shrinks and collapses directly onto the dot.
- **Removed:** Desktop Drag-Selection Box (`#sl-selection-box`) scrapped and removed per user direction.
- **Fixed:** Product Rail ("MOST POPULAR" marquee) — resolved CSS specificity collision where `#page-section-collection .grid-list` with `!important` was overriding the rail track flex styling; added `:not(.sl-rail-track)` to all grid declarations; removed `prefers-reduced-motion` early return from the rail IIFE and handled reduced motion gracefully by pausing auto-scroll while keeping manual drag/swipe active.
- **Added:** Retro Audio Waveform Visualizer inside `#sonarline-product-modal` with click-to-seek and dynamic frequency bar animation.
- **Added:** Audio Player Keyboard Accessibility (`Space`, `ArrowLeft`/`ArrowRight`, `ArrowUp`/`ArrowDown`, `Escape`) + `localStorage` volume persistence.
- **Added:** Card Quick-Listen Trigger on card artwork hover with glowing audio pulse animation.
- **Added:** Cyberpunk Text Scramble Decryption effect on product card title hover.
- **Added:** Product Category Filter HUD Bar (`ARCHIVE // [ ALL ] [ DRUM KIT ] ...`) with live tag aggregation and card filtering without page reloads.

### [2026-09-20 - Release 6]
- **Fixed:** Ticker-over-navbar stacking — padding is now applied to every `.content-main-wrapper, #page-wrap` node (was first-match only, could hit a wrong node and leave the ticker unpadded over the navbar); ticker `z-index` lowered to `1` with navbar forced to `50`; ticker enforcement moved out of the scroll path into one-time `enforceTickerStatic()`.
- **Fixed:** Scroll jank — removed unthrottled `scroll/resize → syncHeaderOffset` listeners (rAF-gated now); card observer no longer watches `style` mutations (was a self-triggering feedback loop) and is 250ms-debounced, ignoring our own decorator nodes; cursor lerp skips hidden tabs.

### [2026-09-20 - Release 5]
- **Fixed:** Reverted ticker to static document flow per user request (`position: relative !important; top: auto !important; left: auto !important; transform: none !important; width: 100% !important; z-index: 10 !important;`). Removed all JS `sticky`/`fixed` assignments in `injectImmediateStyles()`, `syncHeaderOffset()`, and `applyDOMFixes()`. Header engine retains `padding-top` calibration only.
- **Added:** Desktop drag-selection box (`#sl-selection-box`, `pointer:fine` only, `mix-blend-mode: difference`, ignores interactive elements, suppresses native selection while dragging).
- **Added:** Homepage card shortcode background sync (`/b/...`, `/p/...` fetch, `sessionStorage sonarline_prod_<path>`, max 3 concurrent, same-origin only) + `.product-description` token scrubber for Issues 2/3.

### [2026-09-20 - Release 4]
- **Fixed:** Resolved scroll-up navbar overlap on announcement marquee ticker by implementing sticky docking (`position: sticky !important; top: var(--sonarline-header-current-offset); z-index: 999;`) with 140ms smooth transition matching Payhip's native header slide animation.
- **Added:** Dynamic header clearance engine (`initHeaderOffsetEngine`) continuously syncing `--sonarline-header-current-offset` to `Math.max(0, header.getBoundingClientRect().bottom)` across scroll (RAF), window resize, `ResizeObserver`, `MutationObserver` (`.shrink`, `style`), and transition events.
- **Fixed:** Replaced `overflow-x: hidden` with `overflow-x: clip !important; overflow-y: visible !important;` on `.content-main-wrapper` and `.section-wrapper` so sticky positioning is never broken by ancestor scroll containers.
- **Verified:** Tested in Chrome CDP at initial viewport (0px), downward scroll (500px), and upward scroll (150px), confirming the ticker smoothly tracks with the header, sticks cleanly to `top: 0` when scrolled down, and stays completely visible directly beneath the header when scrolled back up.

### [2026-09-20 - Release 3]
- **Fixed:** Suppressed preloader configuration setup section (`#content-section-ZGXLnKKrGq` / `[data-section-key="text-simple"]`) from normal page flow with immediate CSS suppression and robust multi-line token extraction and DOM clearing.
- **Fixed:** Enforced crimson red (`#FF2A2A` / `#FF3B30`) on both the slash (`/`) and the second title word (`.hud-title-second`) in the preloader HUD brand title via high-specificity CSS and inline styling.
- **Fixed:** Removed `cursor: crosshair` from `#sonarline-glitch-title` so the cursor no longer turns into an OS crosshair when hovering over the "SONARLINE" hero text.
- **Hardened:** Preloader fail-safe unmount timer (2.5s) and full `try...catch` lifecycle guards to guarantee normal page interaction under all conditions.

### [2026-09-20 - Release 2]
- **Fixed:** Ticker navbar overlap via dynamic header offset calibration (`syncHeaderOffset` + `--sonarline-header-height: 132px`).
- **Refined:** Converted ticker typography to crisp solid Space Mono with balanced dot separators and calibrated vertical centering.
- **Added:** New preloader shortcode `[[loader-title: FIRST / SECOND]]` with uppercase white `Space Grotesk` and crimson red accent.
- **Added:** New preloader shortcode `[[loader-secondary: TEXT]]` with tracked-out muted `Space Mono`.
- **Added:** Dynamic ascending random incremental progress bar engine (`Math.floor(Math.random() * 20) + 10`) with 200ms hold at 100% and smooth unmount.
- **Updated:** Appended 10 planned items to Section 9 (Future Roadmap & Enhancements).

### [2026-09-20 - Release 1]
- **Fixed:** Repaired terminal syntax highlighter regex leak and HTML entity encoding.
- **Added:** HTML5 Backblaze Audio Player engine with progress scrubber, volume control, and time readout.
- **Added:** Circular `!` info badge on product cards with trigger binding to `#sonarline-product-modal`.
- **Refined:** Rebuilt product cards to match Reference Image 1 (two-row metadata, 1:1 media, conditional tag pills).
- **Fixed:** Stripped all fallback inferred tags (`SOUND LAB`) when `[[tag: ...]]` is not provided.
- **Fixed:** Restored continuous announcement ticker marquee loop with bullet separators.
- **Fixed:** Re-engaged scroll parallax and opacity falloff engine.
- **Logged:** Initialized `FEATURES.md` master tracker for all implemented and upcoming features.
