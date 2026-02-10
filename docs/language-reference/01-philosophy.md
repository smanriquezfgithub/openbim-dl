# OpenBIM Data Language (OpenBIM-DL)
## Language Reference — Version 0.1

---

## Chapter 1 — Philosophy and Mental Model

### 1.1 Purpose of the Language

OpenBIM Data Language (OpenBIM-DL) is a declarative language designed to
**extract, structure, and synthesize training datasets for artificial intelligence
from IFC (STEP) building models**.

The language addresses a specific and currently unresolved problem:

> IFC models contain rich semantic, relational, and geometric information,
> but they are not designed to be consumed directly by AI and machine learning
> training pipelines.

OpenBIM-DL acts as a **formal intermediate layer** between:
- real-world IFC models (heterogeneous, incomplete, tool-dependent),
- and AI-ready datasets (tabular, graph-based, textual, or 3D).

Its goal is not visualization, authoring, or validation of IFC models,
but **systematic and reproducible data generation**.

---

### 1.2 Fundamental Principles

OpenBIM-DL is built on the following non-negotiable principles.

#### 1.2.1 Graph-first Model

The fundamental data model of OpenBIM-DL is a **semantic graph**.

- Each IFC entity is represented as a **node**.
- Each IFC relationship is represented as an **edge**.
- Properties, quantities, and geometry are attributes of nodes, not separate tables.

Tabular, textual, or 3D representations are **derived projections** of this graph,
not the primary model.

This reflects the true nature of IFC data and enables:
- relational reasoning,
- contextual feature extraction,
- detection of structural inconsistencies,
- direct compatibility with graph-based machine learning methods (e.g. GNNs).

---

#### 1.2.2 Declarative by Design

OpenBIM-DL is strictly **declarative**.

Users describe:
- **what data they want**,  
not
- **how to compute it step by step**.

The language intentionally does not expose:
- loops,
- mutable state,
- arbitrary control flow.

This design choice ensures:
- reproducibility,
- optimizable execution plans,
- safe execution in sandboxed SaaS environments.

---

#### 1.2.3 Determinism and Reproducibility

Every OpenBIM-DL execution must be **reproducible**.

By design:
- all randomness requires an explicit seed,
- all outputs generate a manifest,
- all derived values can be traced back to:
  - IFC GlobalIds,
  - the executed recipe,
  - the compiler/runtime version.

OpenBIM-DL is not an exploratory scripting language.
It is a **dataset specification language**.

---

#### 1.2.4 Tolerance to Real-world IFC Data

OpenBIM-DL assumes that real IFC models are imperfect.

Typical conditions include:
- missing properties,
- inconsistent relationships,
- tool-specific modeling patterns,
- partial or invalid geometry.

Therefore:
- `Null` is a first-class value,
- functions do not fail silently or throw runtime errors by default,
- missing or invalid data is explicitly represented and handled.

Validation and quality checks are expressed explicitly through
language constructs (e.g. `qa.*`), not implicit assumptions.

---

### 1.3 Mental Model of Execution

The correct mental model for OpenBIM-DL is as follows:

1. An IFC model is loaded as a `Model`.
2. A semantic graph is constructed internally.
3. One or more **views** select relevant subgraphs.
4. **Features** are derived as pure functions over the graph.
5. Optional **synthetic variation** is applied.
6. The dataset is **partitioned** using explicit anti-leakage rules.
7. One or more **projections** are exported.

This execution order is fixed and intentional.

---

### 1.4 What OpenBIM-DL Is Not

To avoid misuse, OpenBIM-DL is explicitly **not**:

- a general-purpose programming language,
- a BIM authoring or editing tool,
- a full normative IFC validation engine,
- a machine learning framework,
- a visualization or rendering system.

Its scope is strictly **data engineering for AI**.

---

### 1.5 Intended Audience

OpenBIM-DL is intended for:
- BIM professionals working with data analytics and AI,
- researchers applying machine learning to the built environment,
- developers building IFC-based data pipelines,
- platforms requiring safe, declarative dataset generation.

It is not intended for:
- interactive exploratory scripting,
- non-technical BIM users without data context.

---

### 1.6 Relationship to Other Languages

OpenBIM-DL complements, rather than replaces:
- Python (execution and modeling),
- SQL (storage and querying),
- SPARQL (semantic querying),
- general-purpose ETL tools.

Its role is to **formalize and standardize** the transformation
from IFC models to AI-ready datasets.

---

### 1.7 Status of This Specification

This document defines the **normative philosophy and execution model**
of OpenBIM Data Language.

All subsequent chapters of the Language Reference build upon
the concepts established here.

---

