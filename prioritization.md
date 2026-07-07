# Prioritization System (ICE Scoring Model)

To systematically sequence the optimization recommendations for the AP News platform, we implement the **ICE Scoring Model**. This matrix evaluates fixes based on three core internal factors scored from 1 to 10, multiplying them to calculate a final prioritization rank.

$$\text{ICE Score} = \text{Impact} \times \text{Confidence} \times \text{Ease}$$

### 1. Impact (1 - 10)

Measures the performance magnitude of the fix and how aggressively it recovers Core Web Vitals on throttled device connections.

- **10**: Resolves a catastrophic threshold failure (e.g., recovering a multi-second mobile LCP delay).
- **7-9**: Significantly clears main-thread CPU freezes, interaction blockers, or layout shifting.
- **4-6**: Yields healthy, noticeable optimizations to resource delivery weights.
- **1-3**: Minor micro-optimizations with low visual change for the user.

### 2. Confidence (1 - 10)

Measures our technical certainty regarding the root cause baseline and the predictability of the solution's outcome.

- **10**: Explicitly proven by Lighthouse diagnostics, native DOM structures, and mathematical compression models.
- **7-9**: Validated by lab metrics, but involves minor dynamic runtime third-party variables.
- **1-6**: Highly speculative or architecture-dependent fix requiring deep exploratory post-deployment telemetry.

### 3. Ease (1 - 10)

Measures the velocity of engineering implementation. Unlike "Effort," a higher score means the fix is simpler and faster to deploy.

- **10**: Simple, immediate fix (localized CSS changes, HTML attribute tweaks).
- **7-9**: Low complexity implementation requiring minimal asset adjustment.
- **4-6**: Moderate effort requiring build script updates or third-party tag migrations.
- **1-3**: Highly complex overhaul involving deep infrastructure changes or cross-department code audits.
