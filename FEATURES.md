# Sonarline Store Theme — Feature Tracker & Changelog

**Project:** Sonarline Audio Storefront (`https://sonarlineaudio.space`)  
**Platform:** Payhip Custom Theme Injection  
**Repository:** `Th3Tamas/store-theme`  
**Last Updated:** 2026-09-20  

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
  - Multi-element cursor with `mix-blend-mode: difference` inversion.
  - Outer tracking ring with Sonarline orange accent.
  - Smooth physics, hover enlargement on interactive targets (`a`, `button`, `.sonarline-card-info-badge`).
- `[x]` **Desktop Drag-Selection Box (`#sl-selection-box`):**
  - `pointer:fine`-only wireframe rectangle (`mix-blend-mode: difference`), ignores interactive targets, suppresses native selection while active.
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

---

## 9. Future Roadmap & Enhancements

- `[ ]` **Product Detail Page Brutalist Theme:**
  - Extend custom dark wireframe styling, Space Grotesk typography, and embedded Backblaze audio preview player directly to `/b/...` and `/p/...` product pages.
- `[ ]` **Audio Waveform Visualizer:**
  - Replace the standard progress scrubber in the modal with a custom retro waveform / canvas audio visualizer.
- `[ ]` **Product Category Filter Bar:**
  - Client-side tag filtering (e.g. `ALL`, `DRUM KITS`, `SAMPLE PACKS`, `MULTI-KITS`) to filter the homepage card grid without page reloads.
- `[ ]` **Cart Drawer & Checkout Styling Alignment:**
  - Style Payhip's native slide-out cart drawer with matching wireframe borders, dark background, and monospace font hierarchy.
- `[ ]` **Audio Volume Persistence:**
  - Save user volume preferences in `localStorage` across page visits.
- `[ ]` **Keyboard Accessibility for Audio Player:**
  - Add spacebar (play/pause), arrow keys (seek), and escape (close modal) shortcut listeners.
- `[ ]` **Global Sticky Bottom Audio Deck:** Persistent playback when modal is closed or during page navigation.
- `[ ]` **Card Quick-Listen Trigger:** Instant play/pause overlay on card media hover.
- `[ ]` **Audio Stem / Layer Selector in Preview Modal:** Multi-track demo auditioning via `[[audio:stem:url]]`.
- `[ ]` **Multi-Sample Directory Tree:** `[[contents: ...]]` formatted into an ASCII tree breakdown inside modals.
- `[ ]` **Instant In-Page Search & Live Card Filter HUD:** Real-time counter readouts and instant filtering.
- `[ ]` **3D Hardware-Accelerated Perspective Tilt on Card Hover:** Subtle perspective mouse tracking.
- `[ ]` **Monospace Text Scramble / Character Decryption Effect on Hover:** Cyberpunk text reveal animation.
- `[ ]` **Dynamic Web Audio API Oscilloscope / Reactive Scanline Pulse:** Real-time visualizer canvas.
- `[ ]` **Brutalist Terms of Service & Sample Clearance Drawer:** Dedicated slide-over modal for licensing.
- `[ ]` **Floating HUD Cart Counter & Order Total Pill:** Minimalist sticky indicator for cart status.

---

## 10. Changelog & Revision History

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
