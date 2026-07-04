# Performance Audit: Technical Vulnerability Findings — AP News

## Part 1: Performance Metric Analysis Findings

### Finding 1: Bloated Document Delivery & Obsolete Inline Bundling

- **How it affects users:** Mobile users on cellular connections face massive visual startup delays, staring at an empty white viewport for several seconds during initial site assembly.
- **Metric(s) affected:** First Contentful Paint (FCP - 6.9s mobile) and Largest Contentful Paint (LCP).
- **The Cause:** The application document is excessively large because it relies on legacy architectural methods optimized for old HTTP/1 protocols. Specifically, an entire 70 KB static SVG icon map asset is directly inlined into the HTML code, increasing data weight and forcing the browser parsing thread to stall initial render loops.
- **The Solution:** Restructure the primary codebase to exploit modern HTTP/2 protocol advantages. Eliminate large inlined text modules, stop excessive JavaScript/CSS bundling patterns, and offload the 70 KB icon map into an isolated, cached external resource file.

### Finding 2: Lack of Image Fetch Prioritization & Unoptimized Video Blocks

- **How it affects users:** Critical above-the-fold narrative imagery loads slowly or appears late, leaving users unable to quickly tell if the headline content has finished loading.
- **Metric(s) affected:** Largest Contentful Paint (LCP - 40.3s mobile) and Speed Index (SI - 18s mobile).
- **The Cause:** The frontend template lacks priority indicators for above-the-fold content. The main headline image is fetched in parallel with secondary and tertiary assets lower on the page, rather than loading sequentially. Compounding this, the primary hero article asset is an embedded video player that initiates network calls instantly rather than loading a placeholder image.
- **The Solution:** Add explicit `fetchpriority="high"` HTML attributes onto primary, visible article images to prioritize them over secondary resources. Replace autoplaying inline hero video loops with a static preview image container, deferring heavy video rendering scripts until the user explicitly clicks a play trigger.

### Finding 3: Visual Disruption via Unbounded Structural Ad Blocks

- **How it affects users:** The structural layout of the platform appears broken or blank for several seconds upon arrival, as text columns violently shift position once the slow top-of-page advertising units finish loading.
- **Metric(s) affected:** Speed Index (SI), Cumulative Layout Shift (CLS), and First Contentful Paint (FCP).
- **The Cause:** Top-of-page display advertising slots are treated as dynamic runtime blocks that lack fixed dimensions in the layout. Because these scripts execute late in the rendering lifecycle, the layout leaves a blank space that causes content to shift when elements are injected above the fold.
- **The Solution:** Relocate programmatic display blocks lower down the layout tree (below the fold). If a top ad block is strictly necessary for monetization, reserve fixed aspect-ratio container heights using explicit CSS styling rules to prevent content shifts when the ad asset renders.

---

## Part 2: Network Statistics Findings

### Finding 4: Inlined Third-Party Script Bloat & Thread Blocking

- **How it affects users:** The webpage appears visually complete, but freezes or stutters if the user attempts to scroll or tap links right away.
- **Metric(s) affected:** Total Blocking Time (TBT - over 2,100ms on both environments) and Time to Interactive (TTI).
- **The Cause:** The primary page initiates a massive volume of network requests (up to 969 targets on desktop), resulting in over 7.5 MB of JavaScript and CSS code parsing. A high concentration of these scripts are inlined third-party trackers, analytics, and marketing tags that execute synchronously, blocking the browser's main thread.
- **The Solution:** Conduct a tag audit to prune redundant third-party scripts. Shift marketing trackers into modern tag management workflows that employ non-blocking asynchronous script execution (`async` or `defer`), preventing them from competing with core layout rendering loops.

### Finding 5: High Transactional Viewport Instability (Donate Page Deficit)

- **How it affects users:** Users attempting to fill out donation fields or click payment buttons frequently misclick as elements unexpectedly jump around, causing frustration and directly hurting conversion rates.
- **Metric(s) affected:** Cumulative Layout Shift (CLS - measured at a critical **0.76** score).
- **The Cause:** The transactional portal page (`/donate`) loads heavy form scripts, external payment portal elements, and security validation frames dynamically without predefined layout boxes. As these secure modules finish loading across the network, they push the text inputs down, destabilizing the visual hierarchy.
- **The Solution:** Apply explicit minimum height values (`min-height`) via CSS to container elements holding the payment and validation forms. This ensures the layout reserves space for these modules before they finish downloading, stabilizing the page.

### Finding 6: Absolute Binary Asset Compression Deficiency

- **How it affects users:** Mobile data plans are consumed quickly by repetitive downloads, and images load systematically slower over cellular network handshakes.
- **Metric(s) affected:** Speed Index (SI), Data Transfer Volume, and Largest Contentful Paint (LCP).
- **The Cause:** While text assets compress effectively via modern GZIP/Brotli pipelines, binary media representations (accounting for over 14.7 MB on desktop and 7.4 MB on mobile layouts) lack network compression rules. The platform delivers legacy media variants without formatting them into modern containers.
- **The Solution:** Deploy automated server-side transformation pipelines to serve all graphic content in modern compressed containers like WebP or AVIF. This structural conversion reduces overall binary weight by up to 50%-70% without sacrificing visual fidelity.
