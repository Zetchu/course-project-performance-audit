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
