# OpenBIM Data Language (OpenBIM-DL)
## Language Reference — Version 0.1

---

## Chapter 2 — Types and Values

### 2.1 Overview

This chapter defines the **type system** of OpenBIM Data Language (OpenBIM-DL).

The type system is intentionally:
- **simple**, to remain accessible,
- **explicit**, to avoid ambiguity,
- **tolerant**, to reflect real-world IFC data,
- **stable**, to support reproducible datasets.

OpenBIM-DL is **strongly typed but permissive**:  
types are enforced where meaningful, while missing or invalid data is represented explicitly using `Null`.

---

### 2.2 Primitive Types

OpenBIM-DL defines the following primitive types.

#### 2.2.1 Bool
Represents a boolean value.

Allowed values:
- `true`
- `false`

Booleans are commonly produced by:
- predicates,
- validation rules,
- existence checks.

---

#### 2.2.2 Int
Represents a signed integer number.

Used primarily for:
- counts,
- indices,
- discrete categories.

There is no implicit conversion from `Text` to `Int`.

---

#### 2.2.3 Float
Represents a real numeric value.

Used for:
- quantities,
- dimensions,
- coordinates,
- numeric features.

All numeric operations are performed using `Float`.

---

#### 2.2.4 Text
Represents a Unicode string.

Text values may originate from:
- IFC attributes,
- property values,
- generated descriptions,
- templates.

Text comparison is **case-sensitive by default** unless explicitly normalized.

---

#### 2.2.5 Guid
A specialized `Text` type representing an IFC `GlobalId`.

A value of type `Guid` must:
- be a valid IFC GlobalId encoding,
- uniquely identify an IFC entity within a model.

If an entity does not have a valid GlobalId, `Null` is returned instead.

---

#### 2.2.6 Enum
A restricted `Text` value constrained by a vocabulary.

Enums are used for:
- IFC predefined types,
- classification systems,
- controlled feature values.

Invalid enum values are represented as `Null`.

---

### 2.3 Null Value

#### 2.3.1 Definition

`Null` represents the **absence of a value**.

It is a first-class value and is explicitly supported throughout the language.

Common causes of `Null` include:
- missing IFC attributes,
- absent property sets,
- invalid or unresolvable geometry,
- incompatible schema versions.

---

#### 2.3.2 Null Propagation

Unless explicitly handled, `Null` propagates through expressions.

Examples:
- `to_num(Null)` → `Null`
- `geom.bbox()` when no geometry exists → `Null`
- arithmetic with `Null` → `Null`

Null propagation ensures:
- deterministic behavior,
- no hidden assumptions,
- explicit data quality visibility.

---

#### 2.3.3 Null Handling

OpenBIM-DL provides explicit mechanisms to handle `Null`:

- `exists(x)`
- `is_null(x)`
- `coalesce(a, b, ...)`

Implicit defaulting is not allowed.

---

### 2.4 Collection Types

#### 2.4.1 List[T]

An ordered collection of values of type `T`.

Characteristics:
- duplicates are allowed,
- order is preserved,
- may be empty.

Lists are returned by:
- graph navigation functions,
- aggregations,
- tokenization and expansion operations.

---

#### 2.4.2 Set[T]

An unordered collection of unique values of type `T`.

Characteristics:
- duplicates are removed,
- order is not guaranteed,
- may be empty.

Sets are typically used for:
- deduplication,
- categorical aggregation,
- identity comparisons.

---

#### 2.4.3 Map[K, V]

A key–value collection.

Characteristics:
- keys must be unique,
- key type `K` must be `Text` or `Enum`,
- values may be of any type.

Maps are commonly used for:
- templates,
- metadata blocks,
- structured outputs.

---

### 2.5 Vector Types

#### 2.5.1 Vec[n]

A fixed-length numeric vector of dimension `n`.

- All elements are of type `Float`.
- The dimension `n` is part of the type.

Examples:
- `Vec[3]` → coordinates, directions
- `Vec[6]` → bounding boxes
- `Vec[128]` → embeddings or signatures

Vectors are immutable.

---

#### 2.5.2 BBox

A specialized alias for `Vec[6]`.

Structure:
[xmin, ymin, zmin, xmax, ymax, zmax]

Bounding boxes are:
- axis-aligned,
- expressed in model or world coordinates,
- approximate by design.

---

### 2.6 Reference Types

#### 2.6.1 Node

Represents an IFC entity in the semantic graph.

A `Node`:
- corresponds to exactly one IFC instance,
- may have attributes, properties, geometry, and relationships.

Nodes are opaque references; direct mutation is not allowed.

#### 2.6.2 Edge

Represents a relationship between two nodes.

Edges:
- are typed (relation kind),
- may carry metadata,
- are directional where applicable.

Edges are primarily used in graph projections and relational analysis.

---

### 2.7 Type Coercion Rules

OpenBIM-DL uses **explicit coercion only**.

Allowed coercions:
- `Any → Text` via `to_text`
- `Any → Float` via `to_num`

Forbidden:
- implicit numeric conversion from `Text`,
- implicit boolean casting,
- silent enum coercion.

If coercion fails, the result is `Null`.

---

### 2.8 Type Stability and Evaluation Context

All expressions in OpenBIM-DL are evaluated in a **well-defined context**:
- node context (default),
- edge context (for graph projections),
- batch context (for validation and aggregation).

The result type of an expression must be:
- deterministic,
- independent of execution order,
- stable across runs.

---

### 2.9 Summary

The OpenBIM-DL type system prioritizes:
- clarity over cleverness,
- explicitness over convenience,
- robustness over strictness.

This design reflects the realities of IFC data and enables safe,
reproducible, and auditable dataset generation.

---

