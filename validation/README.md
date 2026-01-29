# Validation Artifacts

This directory contains the results of empirical validation tests demonstrating the system's core invariants. These artifacts are published as part of a "Hardware-Style" validation approach: the results are public, but the generation scripts remain private.

## Contents

### Stability Testing
- **[stability_test_report.md](./stability_test_report.md)** - Full results of the noise stability test
- **[stability_test_results.csv](./stability_test_results.csv)** - Raw data for independent analysis

### Boundary Enforcement
- **[boundary_enforcement_report.md](./boundary_enforcement_report.md)** - Gate0 policy override demonstration
- **[boundary_enforcement_trace.csv](./boundary_enforcement_trace.csv)** - Decision trace with enforcement events

### Auditability
- **[auditability_comparison.md](./auditability_comparison.md)** - Black-box vs. Hybrid architecture trace comparison

## Verified Invariants

These tests demonstrate the following system guarantees:

| Invariant | Verification Method |
|-----------|---------------------|
| **Stability** | Output variance bounded under perception noise |
| **Integrity** | Policy violations never propagated downstream |
| **Traceability** | Every decision reconstructible from audit logs |
| **Reproducibility** | Overrides deterministic given input trace |

## Methodology

All tests use synthetic data with explicitly defined input specifications:
- Noise models: Gaussian with varying sigma
- Perturbation models: Linear sensor drift
- Decision thresholds: Documented policy boundaries

**Privacy Note:** Source scripts and internal rule definitions are not published. These artifacts represent the observable behavior of the system, not its implementation.

---

*Last updated: 2026-01-28*
