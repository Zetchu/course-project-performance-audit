# Laboratory Telemetry Baseline — AP News

This document registers the comparative laboratory performance benchmarks across Desktop and Emulated Mobile device environments.

## Environmental Configuration Profile

### Desktop Target Profile

- **Execution Engine:** Lighthouse Core v13.2.0
- **Throttling Profile:** Unthrottled / Clean-Room Cable Connection Baseline

### Mobile Throttled Environment

- **Device Emulation:** Moto G4 User Experience Matrix
- **Network Throttling Preset:** Standard Simulated 4G (150ms RTT, 1.6 Mbps Down, 750 Kbps Up)
- **CPU Throttling Preset:** 4x Main-Thread Processor Slowdown Multiplier

---

## Core Performance Metrics Comparison Matrix

| Core Performance Metric            | Desktop Lab Value | Mobile Homepage | Mobile Standard Article | Performance Evaluation (Mobile)        |
| :--------------------------------- | :---------------- | :-------------- | :---------------------- | :------------------------------------- |
| **First Contentful Paint (FCP)**   | 1.4 s             | 6.9 s           | 4.2 s                   | 🔴 RED (Severe Mobile Parsing Delay)   |
| **Largest Contentful Paint (LCP)** | 2.5 s             | 40.3 s          | 18.4 s                  | 🔴 RED (Catastrophic Visual Failure)   |
| **Speed Index (SI)**               | 3.2 s             | 18.0 s          | 9.5 s                   | 🔴 RED (Critical Visual Lag)           |
| **Time to Interactive (TTI)**      | 3.8 s             | 22.4 s          | 14.1 s                  | 🔴 RED (Main Thread Congestion)        |
| **Total Blocking Time (TBT)**      | 240 ms            | 2,100 ms        | 1,450 ms                | 🔴 RED (Severe Execution Interruption) |
| **Cumulative Layout Shift (CLS)**  | 0.042             | 0.120           | 0.085                   | 🟠 Orange (Moderate Instability)       |

## Networking Footprint (Global Mobile Home Performance)

- **Total Network Request Count:** 969 Requests (Desktop) / 412 Requests (Mobile)
- **Total Transfer Payload Weight:** ~7.5 MB (Uncompressed Core Scripting Bundle Weight)

## Execution, Coverage, & Layer Profiling — AP News

### Code Coverage Profile

- **Critical CSS Extraction:** Completely absent. The main HTML document fails to inline critical, above-the-fold design rules.
- **Unused CSS:** Measured at **84%** of the global stylesheet. This dead payload originates from monolithic layout definitions and utility classes loaded upfront that are only used on deeply nested, alternate category page templates.
- **Unused JavaScript:** Measured at **71%** (over 5.3 MB of the 7.5 MB raw bundle weight). This massive code bloat is driven by heavy, synchronous third-party marketing, analytics, and dynamic tracking bundles loaded globally into the critical rendering path.

### Performance Flame Chart & Scrolling Analysis

- **Frame Drops & Skipping:** Severe and highly persistent. Rapidly scrolling down the dynamic card grid or category feeds on mobile viewports triggers an immediate drop in frames, causing the framerate to fall from a target of 60fps down into the single digits (5-12fps).
- **Primary Cause of Jank:** The DevTools Performance Flame Chart registers severe, repeated Long Tasks (spanning 150ms to 450ms). As the user scrolls, lazy-loading programmatic advertisement nodes are dynamically injected into the DOM, triggering expensive synchronous layout reads (`OffsetHeight` / `getBoundingClientRect`) inside the interaction loops. This induces catastrophic "Layout Thrashing" that stalls the browser's main thread.

### Layers & Animations Profile

- **Paint Layer Count:** Over **280 independent compositor layers** are generated during initial page assembly.
- **Forced Layering (Hacks):** The build framework artificially forces a massive layer explosion by assigning hardware-acceleration properties like `will-change: transform` and `transform: translate3d(0,0,0)` to nearly every nested card wrapper in the feed grids, saturating GPU memory.
- **Animation Triggers & Jank:** The main navigation dropdowns and sliding breaking-news tickers suffer from severe first-frame jank. The Performance panel shows these animations are driven by non-compositor triggers (such as mutating raw `height`, `margin-top`, and `display` values), forcing the rendering pipeline to completely recalculate Layout, Paint, and Composite steps on every single frame.

## Rendering Strategy Analysis — AP News

The AP News platform employs a hybrid architectural rendering model combining Server-Side Rendering (SSR) for editorial content with Client-Side Rendering (CSR) for dynamic feeds, search functions, and interactive widgets.

### Rendering Strategies by Page Type

1. **Domain Homepage & Standard Article Nodes (Hybrid SSR + Heavy Hydration)**
   - **Strategy:** The server pre-renders raw HTML (SSR), dispatching it immediately over the network. Once the client browser parses this shell, a monolithic React/Next.js hydration bundle takes over to attach active event listeners, dynamic advertisement modules, and personalized content grids.
   - **User Impact & Tradeoffs:** While SSR delivers markup to the browser quickly, the heavy client-side hydration payload (~7.5 MB of uncompressed JavaScript) causes a catastrophic "uncanny valley" effect. The page appears visually populated, but the UI is completely unresponsive to touches, clicks, or scrolls for several seconds while the main thread is hijacked by hydration compilation loops.
   - **Assessment:** SSR is a standard choice for SEO-sensitive news hubs, but pairing it with massive, un-split client-side hydration is highly inefficient. The best architectural choice would be **Static Site Generation (SSG) with Partial Hydration (Islands Architecture)**, which renders interactive components (like login forms or live widgets) in isolation without hydrating the static body text.

2. **FIFA World Cup Live Hub & Sports Feeds (SSR + CSR Polling)**
   - **Strategy:** SSR builds the baseline template container, but client-side script layers (CSR) continually poll background APIs to inject live scoreboards, active match stats, and real-time commentary directly into the DOM.
   - **User Impact & Tradeoffs:** Users receive instant, live data updates without manual page refreshes. However, the continuous, un-throttled client-side rendering loops trigger severe CPU exhaustion on mobile devices. The constant DOM insertions force layout recalculation cascades on a loop, generating intense stuttering and jank during viewport scrolls.
   - **Assessment:** A dynamic live feed requires a real-time updating architecture. However, relying on continuous client-side DOM polling and re-renders is a poor implementation. The ideal choice is server-sent events (SSE) or WebSockets paired with a virtual DOM diffing algorithm to ensure only updated score items re-render.

3. **Interactive Hub Quizzes & Search Results Matrix (Client-Side Rendering — CSR)**
   - **Strategy:** These pathways are completely rendered in the user's browser. The initial server delivery is a bare HTML shell, and JavaScript scripts fetch, build, and paint the entire layout dynamically.
   - **User Impact & Tradeoffs:** Because there is no pre-rendered content, users stare at a blank loader screen for up to 6.9 seconds on throttled 4G connections. However, once the JS application is fully booted in memory, transitions, quiz questions, and state updates occur without any additional page transitions.
   - **Assessment:** CSR is appropriate for highly interactive dashboards (like quizzes) where SEO indexation is not required. However, for search result pages, a server-generated search feed would offer a much more responsive initial experience.
