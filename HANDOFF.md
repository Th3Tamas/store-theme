# SONARLINE STORE THEME — SYSTEM HANDOFF & RESUMPTION SPECIFICATION (`HANDOFF.md`)

> **Checkpoint Date:** 2026-09-20  
> **Repository:** `Th3Tamas/store-theme`  
> **Production URL:** [https://sonarlineaudio.space](https://sonarlineaudio.space)  
> **Target Audience:** Incoming Autonomous Coding Agent (OpenCode / Claude Code / Aider) or Lead Systems Engineer

---

## 1. Executive Project Brief & Tech Stack

### 1.1 Overview & Domain Context
- **Platform:** Payhip storefront running on the **Tusk theme** baseline.
- **Production Storefront URL:** `https://sonarlineaudio.space`
- **GitHub Repository:** `Th3Tamas/store-theme` (default branch: `main`).
- **Nature of Project:** Extreme customization of Payhip's closed-source, hosted SaaS CMS via client-side CSS/JS injection and template embeds to transform a stock store into an avant-garde, brutalist sound design archive.
- **Visual & Design DNA:**
  - Heavily inspired by **Atrum Lab** (`atrumlab.com`), Teenage Engineering, and brutalist aerospace telemetry.
  - **Color Palette:** Pure obsidian dark backgrounds (`#080808`, `#0d0d0d`, `#111111`, `#161616`), razor-sharp 1px hairline wireframe borders (`rgba(255, 255, 255, 0.12)` or `rgba(255, 255, 255, 0.18)`), with electric **International Orange** (`#ff5500` / `#ff4400`) and **Crimson Red** (`#FF2A2A` / `#FF3B30`) accents.
  - **Typography:**
    - `Space Grotesk` (Google Fonts): Primary display typography, hero headlines, modal headers, uppercase accents.
    - `Space Mono` (Google Fonts): Technical readouts, terminal syntax, prices, timecodes, ticker items.
    - `Inter` (Google Fonts): Body descriptions, legal copy, UI secondary labels.
  - **Zero-Glow Mandate:** The user strictly prohibits text-shadow glows, fuzzy drop-shadows, or neon halos on typography and dividers. All elements must render crisp, solid, and hairline.

### 1.2 Asset Delivery & CDN Architecture
- Payhip does not allow direct theme file uploads or server-side templating (Liquid/Blade). All overrides operate through Payhip's **Storewide Header / Footer HTML Injection** settings.
- CSS is imported in Payhip via `@import url("https://cdn.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-store-pages-custom.css");` or injected via `<link>` tags.
- JavaScript and dynamic DOM modifications are executed via `storewide-header-html-js-code-injection.html`.
- **CDN Edge Caching:**
  - jsDelivr caches files aggressively. Any push to `main` **must** be followed by a cache purge request:
    ```powershell
    curl.exe -s "https://purge.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-store-pages-custom.css"
    curl.exe -s "https://purge.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-header-html-js-code-injection.html"
    ```
  - Note: jsDelivr enforces rate-limiting/throttling headers (~800ms reset). Always verify JSON response: `{"status": "finished"}`.
  - In addition, the header injection loader uses dynamic cache-busting `?v=` + `Date.now()` when pulling remote raw fragments.

---

## 2. File Structure & Architectural Roles

```
.
├── storewide-store-pages-custom.css           # Primary CSS stylesheet overrides (~2500 lines)
├── storewide-header-html-js-code-injection.html # Master runtime injection: DOM parser, HUD, audio, cursor (~3360 lines)
├── storewide-footer-html-js-code-injection.html # Footer injection slot (currently empty placeholder)
├── all-custom-pages-embed.html                # Legacy embed container (collapses safely to 0px)
├── FEATURES.md                                # Master roadmap, feature status tracker & changelog
└── HANDOFF.md                                 # This operational resumption document
```

### Detailed File Roles:
1. **`storewide-store-pages-custom.css`:**
   - Defines CSS custom properties (`--sonarline-*`), typography imports, dark mode color overrides, CSS grid restructuring.
   - Enforces brutalist card styling, 1:1 square media ratio, two-tier card metadata rows, flush-right pricing.
   - Contains high-specificity ("cascade winner") rules to override Payhip's native inline styles and `editorv2-shop-core.bundle.js` styles.
   - Manages ticker layout, wireframe border treatments, and modal structure.

2. **`storewide-header-html-js-code-injection.html`:**
   - **`injectImmediateStyles()`:** Injects critical `<style>` rules synchronously into `<head>` before DOM ready to prevent Flash of Unstyled Content (FOUC).
   - **HUD Boot Preloader Engine:** Manages the CRT scanline overlay, typing simulation, brand title formatting, dynamic incrementing progress bar, and safe unmount.
   - **CMS Shortcode Parser:** Intercepts Payhip WYSIWYG text across the page, parses custom tokens (`[[tag:...]]`, `[[audio:...]]`, `{{terminal:...}}`, etc.), converts them into DOM elements, and scrubs raw token text from the viewport.
   - **Product Card Re-architect:** Rebuilds Payhip's default collection cards into two-tier wireframe cards with circular `!` modal triggers.
   - **Audio Preview Engine & Modal:** HTML5 audio runtime playing Backblaze B2 streaming audio with timeline scrubber, play/pause state, timecode readout, and volume control.
   - **Kinetic Custom Cursor:** Smooth lerp physics cursor with `mix-blend-mode: difference`, orange tracking dot, and hover scaling.
   - **Header Offset Engine (`syncHeaderOffset` / `initHeaderOffsetEngine`):** Measures Payhip's dynamic fixed/collapsing header and synchronizes CSS variables (`--sonarline-header-height`).

3. **`storewide-footer-html-js-code-injection.html`:**
   - Empty placeholder (`<!-- footer empty -->`). Reserved for deferred third-party scripts or closing tags if needed.

4. **`all-custom-pages-embed.html`:**
   - Legacy embed script that explicitly zeroes out and collapses any section wrappers associated with embed-code sections (`display: none !important; height: 0px !important;`).

5. **`FEATURES.md`:**
   - Central engineering log tracking completed features (`[x]`), in-progress items (`[/]`), active defects (`[!]`), and future roadmap specs (`[ ]`).

---

## 3. Current Live State vs. Broken Regressions

### 3.1 Custom Kinetic Cursor
- **State:** `[x]` Fully implemented and functional.
- **Behavior:** Features a centered orange dot (`#ff5500`) and a larger outer trailing circle with smooth linear interpolation (lerp). Uses `mix-blend-mode: difference` so it inverts dynamically over light/dark surfaces.
- **Hover States:** Automatically expands on interactive targets: `a`, `button`, `input`, `.sonarline-card-info-badge`, and modal triggers.
- **Fixed Regression:** The cursor was previously stuck on `cursor: crosshair` when hovering over `#sonarline-glitch-title` ("SONARLINE"). This was removed and verified.

### 3.2 CMS Shortcode Engine
The parser runs during DOM hydration and intercepts the following custom syntax:
- `[[preloader: <lines>]]`: Multi-line text for the preloader HUD. Prepends `>` prompt and formats `[OK]` status markers with dot leaders (`......... [OK]`).
- `[[loader-title: FIRST / SECOND]]`: Splits brand title around `/`. `FIRST` is rendered in uppercase white `Space Grotesk`, while the slash (`/`) and `SECOND` are rendered in solid crimson red (`#FF2A2A`).
- `[[loader-secondary: TEXT]]`: Monospace subtext below title with wide letter-spacing (`0.25em`).
- `{{terminal: ...}}` or `{{TERMINAL: ...}}`: Renders technical terminal blocks with syntax coloring (green host, cyan command, orange flags).
- `[[tag: CATEGORY]]`: Extracts category pills (e.g., `DRUM KIT`) for the bottom row of product cards.
- `[[audio: URL]]`: Extracts Backblaze B2 streaming MP3/WAV URLs and binds them to the card's `!` info badge preview modal.
- **Regression to Watch:** Shortcodes placed inside product descriptions on individual product pages (`/b/...`, `/p/...`) are currently not scraped into the homepage collection cards unless an asynchronous fetcher is active (see Section 5).

### 3.3 Product Cards
- **State:** `[x]` Two-tier wireframe layout matching reference specs.
- **Top Row:** Uppercase product title on the left; circular wireframe info badge (`!`) on the right.
- **Bottom Row:** Category tag pill on the left (e.g., `DRUM KIT`); price tag flush-right in clean monospace font.
- **Conditional Visibility:** Tag pill is strictly hidden (`display: none !important`) if no explicit `[[tag: ...]]` token exists. Arbitrary fallback tags (e.g., default `SOUND LAB`) have been completely eradicated.

### 3.4 Preloader & HUD Terminal
- **State:** `[x]` Active with fail-safe timer.
- **Homepage Gating:** Executes strictly on the homepage (`/` or `window.location.pathname === '/'`). Suppressed on `/b/...`, `/p/...`, and checkout paths.
- **Counter:** Dynamic pseudo-random incrementing progress bar (`current += Math.floor(Math.random() * 20) + 10`) holding for 200ms at 100% before smooth fade-out.
- **Failsafe:** Wrapped in strict `try...catch` with a 2.5-second hard timeout (`setTimeout`) that force-dismisses `#sonarline-preloader` (`display: none !important;`) and restores `document.body.style.overflow = ''` if any error occurs.

### 3.5 Ticker Status: Fixed vs. Sticky vs. Static Flow (CRITICAL)
- **Background Context:**
  - Release 2: Ticker was obscured behind Payhip's fixed navbar.
  - Release 4: Ticker was made `position: sticky` with dynamic offset calculations tracking `header.getBoundingClientRect().bottom`.
  - **User Reversal (Current Request):** The user explicitly rejected `position: sticky` and `position: fixed`. They demanded that the ticker sit in **normal document flow** (`position: relative !important; top: auto !important;`) so that it sits right below the header on load and **scrolls away naturally with the page**.
- **Current File State:**
  - `storewide-store-pages-custom.css`: Ticker rules have been updated to `position: relative !important; top: auto !important; left: auto !important; transform: none !important; z-index: 10 !important; width: 100% !important;`.
  - `storewide-header-html-js-code-injection.html`: Check `syncHeaderOffset()` and `injectImmediateStyles()` — ensure that runtime JavaScript is NOT dynamically injecting `position: sticky` or `position: fixed` back onto the ticker elements during scroll/resize events!

### 3.6 Desktop Custom Drag-Selection Box (`#sl-selection-box`)
- **State:** `[/]` PENDING IMPLEMENTATION.
- **Spec:** A brutalist wireframe rectangle appearing when a desktop user clicks and drags across the page background:
  - Element `#sl-selection-box` with `position: fixed; pointer-events: none; z-index: 2147483640; display: none;`.
  - Border: `1px solid rgba(255, 255, 255, 0.45);`.
  - Inversion Blend: `mix-blend-mode: difference; background: rgba(255, 255, 255, 1);`.
  - Suppress native browser text selection during active drag (`::selection { background: transparent; }`).
  - Ignore clicks on interactive elements (`a`, `button`, `input`, `.sonarline-card-info-badge`, product cards).
  - Touch guard: desktop only (`@media (pointer: fine)`).

---

## 4. Critical Payhip Theme Quirks & DOM Selectors

### 4.1 Payhip DOM Structure & Protected Selectors
Payhip uses an automated builder framework that will crash or wipe the page if key containers are removed from the DOM:
- **`header#header`, `.site-header`:** Payhip's native navbar. Contains data attributes like `data-section-setting-fixed-position="yes"` and `data-section-setting-fixed-style="scrollBack"`. When the user scrolls down, Payhip adds `.shrink` or transforms the header off-screen.
- **`.content-main-wrapper` (or `main.main-content`, `#page-wrap`):** The master content wrapper.
  - **CRITICAL:** Do NOT remove this container.
  - **Padding Top:** Must have `padding-top: var(--sonarline-header-height, 132px) !important;` so top content (hero / ticker) is not buried beneath the fixed header on page load.
- **`[data-section-key="ticker"]`, `#content-section-QW9Kn45mB6`:** The native marquee ticker section.
- **`#content-section-ZGXLnKKrGq`, `[data-section-key="text-simple"]`:** The CMS section used by admins to store preloader setup text. Must be hidden from display (`display: none !important; height: 0;`) while allowing JS to read its text content.
- **`.card-wrapper`, `.product-card`, `.grid__item`:** Product collection card wrappers.
- **`.product-description`:** Rich text area on `/b/...` and `/p/...` pages.

### 4.2 The `overflow-x: hidden` vs `clip` Hazard
- Payhip's native `editorv2-shop-core.bundle.js` applies `overflow-x: hidden` inline to `html` and `body`.
- `overflow-x: hidden` creates a new scroll container and breaks `position: sticky` on all child elements.
- When sticky behavior is needed, use `overflow-x: clip !important; overflow-y: visible !important;`.
- When in static/relative flow, `overflow-x: clip` or `overflow-x: hidden` are both safe for preventing horizontal bounce.

### 4.3 Anti-FOUC (Flash of Unstyled Content) Requirements
- Payhip loads external scripts asynchronously. If styles are only injected via DOM-ready callbacks, the stock theme flashes for 200–500ms.
- Always inject critical layout overrides synchronously in `injectImmediateStyles()` inside the `<head>`.

### 4.4 CDN Purge Procedure
After any commit to `main`, execute:
```bash
curl.exe -s "https://purge.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-store-pages-custom.css"
curl.exe -s "https://purge.jsdelivr.net/gh/Th3Tamas/store-theme@main/storewide-header-html-js-code-injection.html"
```

---

## 5. Next Immediate Tasks on the Queue

### Task 1: Complete Ticker Static Flow Unpin in JS
- **Problem:** While `storewide-store-pages-custom.css` has `position: relative !important; top: auto !important;`, `storewide-header-html-js-code-injection.html` still has logic in `syncHeaderOffset()` that programmatically sets `ticker.style.setProperty('position', 'sticky', 'important')` and `ticker.style.setProperty('top', visibleBottom + 'px', 'important')`.
- **Action Required:**
  1. In `storewide-header-html-js-code-injection.html`, remove the ticker style assignment inside `syncHeaderOffset()` or change it to:
     ```javascript
     tickers[t].style.setProperty('position', 'relative', 'important');
     tickers[t].style.setProperty('top', 'auto', 'important');
     tickers[t].style.setProperty('left', 'auto', 'important');
     tickers[t].style.setProperty('transform', 'none', 'important');
     tickers[t].style.setProperty('width', '100%', 'important');
     tickers[t].style.setProperty('z-index', '10', 'important');
     tickers[t].style.removeProperty('transition');
     ```
  2. Also check `injectImmediateStyles()` in the same file to ensure no `position: sticky` or `position: fixed` rules are injected into `<head>`.

### Task 2: Implement Desktop Custom Drag-Selection Box (`#sl-selection-box`)
- **Action Required:**
  1. Add CSS in `storewide-store-pages-custom.css`:
     ```css
     #sl-selection-box {
       position: fixed;
       pointer-events: none;
       z-index: 2147483640;
       display: none;
       border: 1px solid rgba(255, 255, 255, 0.45);
       background: rgba(255, 255, 255, 1);
       mix-blend-mode: difference;
     }
     body.sl-selecting,
     body.sl-selecting * {
       user-select: none !important;
       -webkit-user-select: none !important;
     }
     body.sl-selecting ::selection {
       background: transparent !important;
     }
     ```
  2. Add JS module in `storewide-header-html-js-code-injection.html`:
     - Create `#sl-selection-box` element and append to `document.body`.
     - Gate to `@media (pointer: fine)` (ignore mobile touch).
     - On `mousedown`: Ignore if click target is `a`, `button`, `input`, `textarea`, `.sonarline-card-info-badge`, or inside `.card-wrapper`. Record `startX`, `startY`. Add `body.sl-selecting`.
     - On `mousemove`: If active, compute `left = Math.min(x, startX)`, `top = Math.min(y, startY)`, `width = Math.abs(x - startX)`, `height = Math.abs(y - startY)`. If `width > 3 || height > 3`, set `display: block`.
     - On `mouseup`: Set `display: none`, remove `body.sl-selecting`, reset state.

### Task 3: Homepage Cards Shortcode Fetching (Roadmap Issue 2)
- On homepage collection cards, fetch each card's product URL (`/b/...`, `/p/...`) in the background.
- Parse `[[tag: ...]]` and `[[audio: ...]]` from the response HTML.
- Cache in `sessionStorage` (`sonarline_prod_<url>`).
- Render category tag pill and bind the audio URL to the preview modal.

---

## 6. Verification & Testing Checklist
- [ ] Run `git status` and verify clean working tree.
- [ ] Push to `main` and execute jsDelivr cache purges.
- [ ] Open Chrome CDP or desktop browser:
  - Load `https://sonarlineaudio.space`.
  - Scroll down 500px: Ticker must scroll away naturally with the page, NOT remain pinned to viewport.
  - Drag mouse across background: Verify sharp white/gray wireframe box inverts background content via `mix-blend-mode: difference`.
  - Click on product card `!` info badge: Verify modal opens with audio player and accurate metadata.
