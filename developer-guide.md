# Engineering Implementation Guide: AP News

## Reproducing the Baseline

To accurately reproduce the performance bottlenecks and validate your fixes, you must configure your DevTools environment to match the targeted mobile constraints.

1. **Device Emulation:** Moto G4
2. **Network Throttling:** Standard 4G Simulation (150ms RTT, 1.6 Mbps Down, 750 Kbps Up)[cite: 23]
3. **CPU Throttling:** 4x Main-Thread Processor Slowdown[cite: 23]
4. **Target Pages:** Evaluate the global homepage, a standard article template, and the `/donate` transactional portal.

---

## Required Fixes

### 1. Stabilize Transactional Viewport Shifts (`/donate`)

- **The Mechanism:** Dynamic payment portal elements and secure validation frames load asynchronously without predefined CSS layout boxes, displacing form inputs and generating a severe 0.76 CLS score[cite: 23].
- **The Fix:** Apply explicit `min-height` values via CSS to the container elements holding the payment and validation forms to reserve viewport space before the network download completes[cite: 23].
- **Effort:** Local. Extremely simple front-end CSS adjustment.
- **Validation:** The DevTools Experience panel will register a 0.00 CLS score during the payment portal load sequence.

### 2. Prioritize LCP Headline Assets

- **The Mechanism:** The 40.3-second LCP failure occurs because the browser network timeline downloads secondary programmatic ad layers and sub-grid trackers concurrently, starving the primary hero asset of bandwidth[cite: 23]. Additionally, embedded hero video players initiate instant network calls[cite: 23].
- **The Fix:**
  - Inject `fetchpriority="high"` onto the primary, above-the-fold article images[cite: 23].
  - Replace inline autoplaying hero videos with a static preview image container, deferring the video payload until a user interaction (click-to-play) occurs[cite: 23].
- **Effort:** Local. Markup tagging and component adjustments.
- **Validation:** LCP metrics will drop drastically in the Lighthouse performance trace, and the hero image will load prior to third-party ad blocks.

### 3. Resolve Hydration and Script Thread Blocking

- **The Mechanism:** The platform executes a monolithic hydration cycle (~7.5 MB bundle) and synchronously runs heavy third-party scripts (like Teads rich-media ad wrappers), causing a 2,100ms Total Blocking Time (TBT)[cite: 23].
- **The Fix:**
  - **Structural:** Migrate from full-page hydration to Partial Hydration (Islands Architecture) to prevent static text blocks from executing client-side JS[cite: 23]. Implement code splitting to break up the 7.5 MB bundle[cite: 23].
  - **Local:** Wrap third-party display networks in `IntersectionObserver` to trigger execution only as layout slots approach the active viewport[cite: 23].
- **Effort:** High/Structural for hydration; Medium/Local for tag management.
- **Validation:** TBT will drop below 300ms, and the Performance Flame Chart will no longer show massive 150ms+ Long Tasks during the initial parse.

### 4. Eliminate GPU Saturation and Layout Thrashing

- **The Mechanism:** Scrolling triggers severe Layout Thrashing because lazy-loaded ad scripts read DOM geometry synchronously[cite: 23]. Concurrently, over 280 paint layers are artificially forced via `transform: translate3d(0,0,0)` and `will-change: transform` on static feed cards[cite: 23]. Ticker animations manipulate `margin-top` and `height`, forcing constant repaints[cite: 23].
- **The Fix:**
  - Strip `will-change` and hardware-acceleration properties from static elements[cite: 23].
  - Refactor UI transitions to exclusively use compositor-friendly properties (`transform` and `opacity`)[cite: 23].
  - Batch DOM read/write operations using `requestAnimationFrame`.
- **Effort:** Local. Target CSS transitions, layer declarations, and scroll-handler debouncing.
- **Validation:** The DevTools Layers panel will show a drastically reduced layer count, and the Flame Chart will confirm 60fps scrolling without "Layout Thrashing" flags.

---

## Domains Evaluated but Out of Scope

- **Offline Support (Service Workers):** A robust offline PWA state was considered, but deemed out of scope. As a real-time news syndication platform, serving cached/stale editorial data offers negative utility to the core business model.
- **Secure Contexts:** The platform is already enforcing strict HTTPS routing across all audited nodes.
