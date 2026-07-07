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
