# Auditability Comparison
## Black-Box vs. Hybrid Architecture Decision Traces

**Test Date:** 2026-01-28

This document demonstrates the auditability difference between a traditional neural network "black-box" output and the hybrid Fuzzy Logic + Gate0 architecture.

---

### Invariant Under Test

> **Given:** Any decision produced by the hybrid architecture
>
> **Assert:** The decision is 100% reconstructible from its audit trace
>
> **Assert:** Rule activations are deterministic given inputs
>
> **Assert:** No "hidden" logic exists outside the logged trace

---

## Scenario: Critical Decision Required

**Input:** Patient vitals indicate potential early-stage sepsis.
- Temperature: 38.2C (elevated)
- Heart Rate: 105 BPM (elevated)
- Blood Pressure: 92/58 (low)

---

## Black-Box Model Output

```
Model: SepsisRiskNN_v2.3
Input: [38.2, 105, 92, 58]
Output: 0.847

Prediction: HIGH RISK
Confidence: 84.7%
```

### What the clinician sees:
> "The AI says the patient is high-risk for sepsis."

### What the clinician can audit:
- Nothing. The 0.847 score is the result of matrix multiplications across 2.4M parameters.
- No explanation of which input contributed most.
- No way to verify the logic without retraining or extensive XAI post-hoc analysis.

---

## Hybrid Architecture Output

```
DECISION TRACE: Request ID 0x7A3F
Timestamp: 2026-01-28T14:32:07.123Z

=== PERCEPTION LAYER ===
Input: {temp: 38.2, hr: 105, bp_sys: 92, bp_dia: 58}

Membership Fuzzification:
  temperature:
    - NORMAL: 0.0
    - ELEVATED: 0.85
    - HIGH: 0.15
  heart_rate:
    - NORMAL: 0.2
    - ELEVATED: 0.8
    - HIGH: 0.0
  blood_pressure:
    - LOW: 0.75
    - NORMAL: 0.25
    - HIGH: 0.0

=== REASONING LAYER ===
Rule Evaluation:

  [RULE_0x12] IF temp IS ELEVATED AND hr IS ELEVATED THEN risk IS MODERATE
    Activation: min(0.85, 0.8) = 0.80

  [RULE_0x17] IF bp IS LOW AND temp IS ELEVATED THEN risk IS HIGH
    Activation: min(0.75, 0.85) = 0.75

  [RULE_0x1A] IF bp IS LOW AND hr IS ELEVATED THEN risk IS HIGH
    Activation: min(0.75, 0.8) = 0.75

Aggregated Risk Score: 0.847 (Centroid Defuzzification)

=== POLICY LAYER (Gate0) ===
Policy: sepsis_response_protocol_v1
Constraint Check:
  - risk_score > 0.7 AND bp_low = TRUE -> ESCALATE_TO_PHYSICIAN
  - Result: POLICY_TRIGGERED

Final Action: ESCALATE_TO_PHYSICIAN
Audit ID: TRACE_7A3F_20260128_143207
```

### What the clinician sees:
> "The AI flagged the patient as high-risk for sepsis. **Rule 0x17 and Rule 0x1A fired because the blood pressure is low and both temperature and heart rate are elevated.** Policy requires physician escalation."

### What the clinician can audit:
- Exactly which rules fired
- The relative contribution of each input
- The policy that triggered the action
- A unique trace ID for full reconstruction

---

## Comparison Summary

| Dimension | Black-Box Model | Hybrid Architecture |
|-----------|-----------------|---------------------|
| **Output Format** | Single score | Structured trace |
| **Rule Attribution** | None | Full rule activation log |
| **Input Contribution** | Unknown | Membership degrees visible |
| **Policy Enforcement** | Implicit (baked into training) | Explicit (Gate0 log) |
| **Post-Incident Audit** | Forensic weight analysis | Direct log replay |
| **Modifiability** | Retrain entire model | Edit single rule |

---

## Invariant Verification

- [x] Every decision is 100% reconstructible from its audit trace
- [x] Rule activations are deterministic given inputs
- [x] Policy actions are logged with unique identifiers
- [x] No "hidden" logic exists outside the trace

---

*This document demonstrates the auditability properties of the hybrid architecture using a synthetic clinical scenario. No patient data was used.*
