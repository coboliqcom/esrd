# CobolIQ Diagnostic Pack: Medicare ESRD & IPPS Deterministic Ground Truth

[![License: Proprietary Audit Output](https://img.shields.io/badge/License-Proprietary_Audit_Output-blue.svg)](https://coboliq.com)
[![Engine: CobolIQ AST-SLS](https://img.shields.io/badge/Engine-CobolIQ_AST--SLS_Synaptic_Topology-emerald.svg)](https://coboliq.com)
[![Determinism: 100% Zero-LLM](https://img.shields.io/badge/Determinism-100%25_Zero--LLM_Hallucination-purple.svg)](https://coboliq.com)

> **Ground Truth Execution Specification & Algebraic Lineage Extraction**  
> *Extracted deterministically from US Federal Healthcare CMS Pricer Core (COBOL / CICS / Batch) in <0.8s.*

---

## 🏛️ Executive Summary

For over three decades, the **US Federal Medicare End-Stage Renal Disease (ESRD)** and **Inpatient Prospective Payment System (IPPS)** pricing calculations have governed hundreds of billions of dollars in annual healthcare reimbursement. 

When organizations attempt to modernize these systems using generic LLMs (ChatGPT, Copilots) or manual line-by-line Java rewrites (e.g., CMS Java Pricer), they consistently encounter three fatal architectural traps:
1. **The 2,500-line `BigDecimal` Sprawl:** Bloating a simple 1-line mathematical formula into dozens of nested class files and object allocations.
2. **Residual Memory Ghost Detonations:** Relying on uninitialized `WORKING-STORAGE` static RAM buffers that silently masked bugs on single-threaded z/OS mainframes but cause catastrophic multi-threaded data bleeding in cloud JVMs.
3. **Floating-Point & Rounding Divergence:** Subtle pennies-per-claim rounding discrepancies that compound across millions of patients into hundreds of millions of dollars in audit liabilities.

This repository publishes the **complete algebraic ground truth and memory diagnostic pack** generated deterministically by the **CobolIQ Engine**.

---

## 📦 Diagnostic Pack Structure

| Section | Document | Key Technical Coverage |
|---|---|---|
| **01** | [`01_BUSINESS_RULES_ALGEBRA.md`](./01_BUSINESS_RULES_ALGEBRA.md) | Closed-form algebraic formulas, Du Bois BSA equation, Outlier cutoff mathematics, Comorbidity multipliers. |
| **02** | [`02_MEMORY_GHOSTS_AND_REDEFINES_OVERLAY.md`](./02_MEMORY_GHOSTS_AND_REDEFINES_OVERLAY.md) | `REDEFINES` byte-offset memory collisions, residual RAM buffer ghosts, and multi-threaded JVM singletons. |
| **03** | [`03_PRECISION_AND_ROUNDING_DISCREPANCIES.md`](./03_PRECISION_AND_ROUNDING_DISCREPANCIES.md) | `COMP-3` packed decimal behavior vs IEEE 754 float drift and `BigDecimal.ROUND_HALF_UP` boundaries. |
| **04** | [`04_STRUCTURAL_ISSUES_MANIFEST.md`](./04_STRUCTURAL_ISSUES_MANIFEST.md) | Formal issue catalog mapping 120 calculation gates, dispatch anomalies, and modernization hazards. |
| **05** | [`05_CARDDEMO_CICS_RUNTIME_SPEC.md`](./05_CARDDEMO_CICS_RUNTIME_SPEC.md) | AWS CardDemo CICS bare-metal replacement architecture, 457k TPS benchmark, and [17 BMS `.ini` manifests](./bms_schemas/). |

---

## ⚡ The CobolIQ Distinction: Algebraic Truth vs. Rewritten Noise

```mermaid
graph LR
    classDef legacy fill:#1e293b,stroke:#38bdf8,stroke-width:2px,color:#fff;
    classDef bloat fill:#991b1b,stroke:#ef4444,stroke-width:2px,color:#fff;
    classDef truth fill:#065f46,stroke:#10b981,stroke-width:2px,color:#fff;

    subgraph LEGACY ["1. COBOL Source (10,000+ Lines)"]
        A1["P368ESRD.cbl:L1240<br/>MOVE SPACES TO WS-HOLD<br/>PERFORM 3600-CALC-OUTLIER<br/>COMPUTE H-DRG-WT ROUNDED"]:::legacy
    end

    subgraph FAILED_JAVA ["2. Manual Java / LLM Rewrite (Bloated)"]
        B1["2,500 Lines of BigDecimal<br/>50+ Classes, Factory Beans<br/>12ms Latency, Memory Leaks"]:::bloat
    end

    subgraph COBOLIQ_TRUTH ["3. CobolIQ Closed-Form Truth"]
        C1["Final Algebraic Formula:<br/>Rate = BaseRate × WageIdx × (1 + ΣMultipliers)<br/>0.37μs Latency, Zero Hallucination"]:::truth
    end

    A1 -->|Manual / AI Rewrite| B1
    A1 -->|CobolIQ AST-SLS Engine| C1
```

---

## 🔒 Intellectual Property & Clean-Room Verification

- **Engine Isolation:** This repository contains **purely generated analytical and diagnostic artifacts**.
- **No Proprietary Source Code Leaked:** No CobolIQ AST compiler internals, manifold tensor coordinates, or proprietary source code are exposed.
- **Air-Gapped Compliance:** Generated 100% on air-gapped workstations without external LLM API telemetry or network call-home dependencies.

---

*(C) 2026 CobolIQ. All rights reserved. Deterministic Modernization & Sovereign Legacy Diagnostics.*
