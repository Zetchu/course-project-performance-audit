# Performance Audit: Telemetry Baseline Data — AP News

## 1. Field Core Web Vitals (Real User Monitoring)

### 🖥️ Desktop

- **Largest Contentful Paint (LCP):** 3.7 s
- **Interaction to Next Paint (INP):** 156 ms
- **Cumulative Layout Shift (CLS):** 0.03
- **First Contentful Paint (FCP):** 2.0 s
- **Time to First Byte (TTFB):** 0.2 s

### 📱 Mobile

- **Largest Contentful Paint (LCP):** 2.9 s
- **Interaction to Next Paint (INP):** 179 ms
- **Cumulative Layout Shift (CLS):** 0.06
- **First Contentful Paint (FCP):** 2.0 s
- **Time to First Byte (TTFB):** 0.2 s

---

## 2. Laboratory PageSpeed Insights Telemetry (Homepage `/`)

### 🖥️ Desktop Scores

- **Overall Performance Score:** 27 (🔴 RED ZONE)
- **Accessibility:** 76 | **Best Practices:** 54 | **SEO:** 85
- **Metrics:**
  - **First Contentful Paint (FCP):** 2.2 s
  - **Largest Contentful Paint (LCP):** 13.9 s
  - **Speed Index (SI):** 9.6 s
  - **Total Blocking Time (TBT):** 2,160 ms
  - **Cumulative Layout Shift (CLS):** 0

### 📱 Mobile Scores

- **Overall Performance Score:** 27 (🔴 RED ZONE)
- **Accessibility:** 76 | **Best Practices:** 77 | **SEO:** 85
- **Metrics:**
  - **First Contentful Paint (FCP):** 6.9 s
  - **Largest Contentful Paint (LCP):** 40.3 s (🔴 CRITICAL LATENCY)
  - **Speed Index (SI):** 18.0 s
  - **Total Blocking Time (TBT):** 2,110 ms
  - **Cumulative Layout Shift (CLS):** 0.023

---

## 3. Network Activity Profile (Primary Page Metrics)

### Environmental Configuration Anchors

- **Connection Protocol:** HTTP/2
- **Caching Policy Configuration:** First-party HTML page cache set at a short 2 minutes; static design elements use cache fingerprints with a 1-year Time-To-Live (TTL).
- **Compression Protocols:** Text code elements deploy GZIP/Brotli compression (achieving a healthy 50%-60% network size reduction yield); raw binary files use no compression parameters.

### 🖥️ Desktop Network Activity Footprint

- **Total Requests Made:** 969 over the wire.
- **Total Network Transferred Data:** 24.9 MB (representing a 49.8 MB raw uncompressed total resource footprint size).
- **Payload Allocation Breakdown:**
  - **JS / CSS Layout Scripts:** 7.54 MB.
  - **Graphic/Media Assets:** 14.72 MB.
- **Soft Refresh Footprint:** 219 KB transferred (retaining an uncompressed resource size profile of 1,949 KB via cache rules).

### 📱 Mobile Network Activity Footprint

- **Total Requests Made:** 616 over the wire.
- **Total Network Transferred Data:** 15.1 MB (representing a 37.4 MB raw uncompressed total resource footprint size).
- **Payload Allocation Breakdown:**
  - **JS / CSS Layout Scripts:** 6.09 MB.
  - **Graphic/Media Assets:** 7.47 MB.
- **Soft Refresh Footprint:** 854 KB transferred (retaining an uncompressed resource size profile of 38.1 MB due to local caching rules).
