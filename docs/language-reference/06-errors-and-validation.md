# OpenBIM Data Language (OpenBIM-DL)
## Language Reference — Version 0.1

---

## Chapter 6 — Errors and Validation

### 6.1 Overview

This chapter defines the **error model**, **validation mechanisms**, and
**quality assurance principles** of OpenBIM Data Language (OpenBIM-DL).

OpenBIM-DL is designed to operate on **imperfect, heterogeneous IFC data**.
As such, it distinguishes clearly between:
- **structural errors** (language-level),
- **semantic data issues** (model-level),
- **quality conditions** (analysis-level).

Only structural errors invalidate a document.
All other issues are surfaced explicitly as data signals.

---

### 6.2 Error Categories

OpenBIM-DL defines three categories of errors.

---

#### 6.2.1 Structural Errors

Structural errors prevent a document from being evaluated.

Examples:
- invalid grammar,
- unknown blocks,
- unknown functions,
- duplicate feature names within a `derive` block,
- invalid block ordering,
- type violations that cannot be resolved.

Structural errors:
- are detected at parse or compile time,
- must be fixed before execution,
- stop evaluation immediately.

---

#### 6.2.2 Semantic Data Issues

Semantic issues originate from the IFC data itself.

Examples:
- missing attributes or property sets,
- invalid or inconsistent relationships,
- unresolved geometry,
- incompatible schema versions.

Semantic issues:
- never stop execution,
- result in `Null` values or empty collections,
- are expected and common in real-world data.

---

#### 6.2.3 Quality Conditions

Quality conditions represent **domain-level expectations**
rather than errors.

Examples:
- a door without a host wall,
- an element without spatial containment,
- duplicated identifiers,
- quantities outside reasonable ranges.

Quality conditions are evaluated explicitly using validation functions.

---

### 6.3 Null as a First-Class Signal

`Null` is the primary mechanism by which OpenBIM-DL represents data issues.

Design principles:
- `Null` is explicit and observable,
- `Null` propagates deterministically,
- `Null` never triggers implicit defaults.

This ensures:
- transparency of data quality,
- reproducibility of results,
- auditability of assumptions.

---

### 6.4 Validation Functions (`qa`)

OpenBIM-DL provides a dedicated set of validation and QA functions.

These functions:
- return `Bool`,
- do not mutate data,
- may be evaluated in node or batch context.

---

#### 6.4.1 qa.required(expr) → Bool

Returns `true` if the expression evaluates to a non-`Null` value.

Used to assert presence of required data.

---

#### 6.4.2 qa.in_range(x: Float, min: Float, max: Float) → Bool

Returns `true` if `x` lies within the specified range.

Commonly used for geometric and quantity validation.

---

#### 6.4.3 qa.matches(x: Text, pattern: Text) → Bool

Returns `true` if `x` matches the given pattern.

Used for naming conventions and classification checks.

---

#### 6.4.4 qa.unique(featureName: Text) → Bool

Batch-level validation.

Returns `true` if all values of the given feature are unique.

---

#### 6.4.5 qa.consistent_relation(kind: Text, min: Int, max: Int) → Bool

Returns `true` if the number of relations of the given kind
falls within the specified bounds.

Example:
- a door should have exactly one filling relationship.

---

### 6.5 Validation Semantics

Validation expressions:
- are evaluated after derivation,
- may be evaluated before or after synthesis,
- do not affect export unless explicitly referenced.

Validation results are ordinary boolean features
and may be exported like any other data.

---

### 6.6 Reporting and Interpretation

OpenBIM-DL does not prescribe how validation results are reported.

Instead, it guarantees that:
- validation outcomes are explicit,
- validation logic is reproducible,
- validation rules are part of the document.

Runtimes may choose to:
- generate reports,
- highlight failing entities,
- block exports conditionally.

Such behavior is outside the language specification.

---

### 6.7 No Implicit Enforcement

OpenBIM-DL deliberately avoids implicit enforcement.

Specifically:
- no automatic filtering based on validation,
- no silent data correction,
- no hidden assumptions.

All enforcement must be:
- explicit,
- visible in the document,
- reproducible across runs.

---

### 6.8 Validation as Data

A core principle of OpenBIM-DL is that **validation results are data**.

This enables:
- training anomaly detection models,
- supervised learning on data quality,
- benchmarking model health over time.

Validation is not an afterthought, but a first-class use case.

---

### 6.9 Summary

OpenBIM-DL separates concerns cleanly:
- structural correctness belongs to the language,
- semantic correctness belongs to the data,
- quality expectations belong to the user.

By making validation explicit, deterministic, and observable,
OpenBIM-DL enables robust, auditable, and AI-ready
data pipelines from real-world IFC models.

---
