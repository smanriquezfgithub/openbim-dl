# OpenBIM Data Language (OpenBIM-DL)
## Language Reference — Version 0.1

---

## Chapter 5 — Execution Model

### 5.1 Overview

This chapter defines the **execution model** of OpenBIM Data Language (OpenBIM-DL).

The execution model specifies:
- how blocks are evaluated,
- how contexts are established,
- how determinism is preserved,
- how batch operations are handled.

OpenBIM-DL is **not an imperative language**.
Execution is defined as a **deterministic evaluation of declarations**
over a semantic graph derived from an IFC model.

---

### 5.2 Execution Phases

An OpenBIM-DL document is evaluated in the following fixed phases:

1. **Source initialization**
2. **View resolution**
3. **Feature derivation**
4. **Synthesis (optional)**
5. **Dataset splitting (optional)**
6. **Export materialization**

Each phase must complete before the next one begins.

---

### 5.3 Contexts

All expressions in OpenBIM-DL are evaluated within a **context**.

Three execution contexts are defined.

---

#### 5.3.1 Node Context

The default context.

- Each expression is evaluated once per node in the active view.
- Functions such as `ifc.*`, `pset.*`, `geom.*`, and graph navigation
  operate on the current node.
- Derived features are bound to the node instance.

Example:
height = geom.dims()[2]


---

#### 5.3.2 Edge Context

Used when a view selects edges instead of nodes.

- The current context is an `Edge`.
- Only edge-safe functions may be used.
- Node references must be accessed explicitly.

Edge contexts are primarily intended for graph exports and GNN workflows.

---

#### 5.3.3 Batch Context

Used for operations that require **global knowledge** of the dataset.

Batch context applies to:
- split strategies,
- balancing and sampling,
- validation and QA checks,
- statistics-based normalization.

Batch context evaluation occurs **after derivation** and **before export**.

---

### 5.4 View Evaluation

Each `view` block defines an **independent evaluation domain**.

- Views do not share state.
- Derived features are recomputed per view.
- The same IFC entity may appear in multiple views.

View evaluation order does not affect results.

---

### 5.5 Derivation Semantics

The `derive` block is evaluated as follows:

- Each feature expression is evaluated independently.
- Feature order does not matter.
- Feature values are immutable once computed.
- Errors result in `Null`, not execution failure.

Derived features form a **logical table** where:
- rows correspond to context items,
- columns correspond to feature names.

---

### 5.6 Null and Error Handling

OpenBIM-DL does not throw runtime errors for data issues.

Instead:
- missing or invalid values yield `Null`,
- invalid coercions yield `Null`,
- unavailable relations yield empty collections.

Only **structural errors** (invalid grammar, unknown functions)
invalidate a document.

This design ensures robustness against imperfect IFC data.

---

### 5.7 Determinism and Reproducibility

OpenBIM-DL executions must be **fully deterministic** given:

- the same IFC input,
- the same OpenBIM-DL document,
- the same random seed (if synthesis is used).

All stochastic operations must:
- use an explicit or implicit seed,
- be reproducible across runs,
- be isolated to the `synthesize` phase.

No hidden randomness is allowed.

---

### 5.8 Synthesis Evaluation

The `synthesize` block is evaluated **after derivation**.

Rules:
- synthesis operates on derived features, not raw IFC data,
- operations are applied declaratively,
- original values remain accessible for auditing.

Synthesis may:
- introduce controlled noise,
- simulate missingness,
- rebalance datasets.

Synthesis never alters identity or graph topology.

---

### 5.9 Split Semantics

Splitting is evaluated after synthesis.

- Split keys are computed in batch context.
- All records sharing the same key are kept together.
- Splits are stable and reproducible.

Split strategies are mandatory for train/validation/test workflows
to avoid information leakage.

---

### 5.10 Export Evaluation

Exports consume the **final evaluated dataset**.

Rules:
- exports do not modify data,
- multiple exports may coexist,
- export format does not affect upstream evaluation.

Export materialization is the final phase.

---

### 5.11 Execution Independence

An OpenBIM-DL document:
- does not depend on execution order of blocks beyond phase rules,
- does not depend on runtime parallelism,
- produces identical logical results regardless of implementation details.

This allows:
- CLI runtimes,
- cloud services,
- embedded engines,
to remain interoperable.

---

### 5.12 Summary

The OpenBIM-DL execution model is designed to be:
- deterministic,
- auditable,
- reproducible,
- resilient to imperfect BIM data.

By separating **what** is defined from **how** it is executed,
OpenBIM-DL establishes a stable contract between
BIM semantics and AI data pipelines.

---
