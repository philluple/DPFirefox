# DPFirefox
# On-Device Accounting Enhancements for Private Attribution

This document outlines proposed enhancements to Mozilla Gecko’s Private Attribution module to further improve user privacy through on-device data handling, differential privacy, and privacy-preserving reporting protocols like Prio.

---

## Overview

**On-Device Accounting** refers to storing and processing attribution data (e.g. ad impressions and conversions) directly on the user's device, reducing reliance on external servers and improving privacy.

---

## Background

### Private Attribution Module

This module enables:
- **Ad Impression Tracking** — Counts how often ads are shown.
- **Conversion Measurement** — Tracks actions users take after seeing or clicking ads.

### Supporting Components

| Component | Description |
|----------|-------------|
| `PrivateAttributionService` | Tracks impressions/conversions, stores data in `IndexedDB`, and handles reporting via DAP telemetry. |
| `PrivateAttributionIPCUtils` | Serialization/deserialization for inter-process communication. |
| `Components.conf` | Registers Private Attribution components with Gecko. |
| `Moz.build` | Build rules for the Private Attribution module. |

---

## Goals

1. Integrate **Differential Privacy** to prevent leakage of sensitive data.
2. Create an **On-Device Data Storage Manager** to modularize local data access.
3. Explore **Prio Protocol Integration** to support secure, aggregated telemetry reporting.

---

## Proposed Enhancements

### 1. Differential Privacy Integration

Apply noise to attribution data at two possible stages:

- **Before Storage in IndexedDB**
  - Pros: Prevents inference attacks on stored raw data.
  - Cons: Decreases fidelity of stored information.

- **After Report Generation**
  - Pros: Higher data utility; more accurate for analytics.
  - Cons: Raw data in local storage may be vulnerable.

**Implementation Tasks:**
- Build `DifferentialPrivacy` module with configurable parameters (ε, δ).
- Integrate with `PrivateAttributionService` for pre-storage and post-report use.

---

### 2. On-Device Data Storage Manager

A dedicated module to handle all local attribution data operations.

**Responsibilities:**
- Abstract `IndexedDB` operations.
- Enforce consistency and modular privacy logic.
- API examples:
  - `storeImpression(event)`
  - `storeConversion(event)`
  - `queryReports(filters)`

---

### 3. Prio-Based Reporting

**Prio** is a protocol for private, aggregated data collection.

**Advantages:**
- Prevents cross-site tracking and raw data exposure.
- Strong privacy guarantees for telemetry reports.

**Implementation Notes:**
- Extend `PrivateAttributionService` to batch and encode data for Prio.
- Coordinate with Mozilla telemetry backend for aggregation support.

---

## Tradeoffs

| Approach | Pros | Cons |
|---------|------|------|
| Pre-storage DP | Strong early protection | Data utility loss |
| Post-report DP | Higher utility | Storage risk |
| Storage Manager | Modular, testable | Initial overhead |
| Prio Protocol | Strongest privacy | Higher complexity |

---

## Privacy Considerations

- Avoid persistent storage of rare or sensitive data.
- Never store raw cross-site identifiers.
- All telemetry reporting must comply with Mozilla’s privacy and data collection policies.

---

## Next Steps

- [ ] Define data schema for impressions and conversions
- [ ] Implement `DifferentialPrivacy` module
- [ ] Create On-Device Data Storage Manager
- [ ] Add noise injection hooks
- [ ] Evaluate Prio feasibility for reporting
- [ ] Write integration tests for all components

---

## Glossary

| Term | Definition |
|------|------------|
| **IndexedDB** | Browser-based database for structured data |
| **DAP Telemetry** | Mozilla’s platform for anonymous data reporting |
| **Differential Privacy** | Adds mathematical noise to data to preserve anonymity |
| **Prio** | Protocol for secure data aggregation |
| **Cross-Site** | Interaction between different domains (e.g., `ads.com` → `shop.com`) |

---

## Contributors

Feel free to open a PR or issue if you'd like to contribute to this privacy-preserving initiative.
