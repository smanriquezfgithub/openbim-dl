# OpenBIM Data Language (OpenBIM-DL)
## Language Reference — Version 0.1

---

## Chapter 4 — Functions Catalog

### 4.1 Overview

This chapter defines the **built-in function catalog** of OpenBIM Data Language (OpenBIM-DL).

Functions are:
- deterministic,
- side-effect free (except within `synthesize`),
- evaluated within a well-defined context (node, edge, or batch).

The catalog is designed to:
- reflect IFC’s graph-native structure,
- expose BIM semantics without scripting,
- enable reproducible AI dataset generation.

Unless stated otherwise, all functions return `Null` when their result
cannot be determined.

---

### 4.2 Core and Utility Functions

#### 4.2.1 guid() → Guid | Null  
Returns the IFC `GlobalId` of the current node.

Used for identity, joins, traceability, and split strategies.

---

#### 4.2.2 id() → Text  
Returns a stable internal identifier.

This identifier is runtime-specific and is **not** an IFC attribute.

---

#### 4.2.3 exists(x: Any) → Bool  
Returns `true` if `x` is not `Null` and not empty.

---

#### 4.2.4 is_null(x: Any) → Bool  
Returns `true` if `x` is `Null`.

---

#### 4.2.5 coalesce(a: Any, b: Any, …) → Any  
Returns the first non-`Null` value.

---

#### 4.2.6 to_text(x: Any) → Text | Null  
Explicit conversion to `Text`.

---

#### 4.2.7 to_num(x: Any) → Float | Null  
Explicit conversion to `Float`.

---

### 4.3 IFC Introspection Functions

#### 4.3.1 ifc.type() → Text  
Returns the IFC entity type name (e.g. `IfcWall`).

---

#### 4.3.2 ifc.schema() → Text  
Returns the IFC schema version.

---

#### 4.3.3 ifc.attr(name: Text) → Any | Null  
Returns a raw STEP attribute value.

---

#### 4.3.4 ifc.name() → Text | Null  
Shortcut for `ifc.attr("Name")`.

---

#### 4.3.5 ifc.predefined() → Enum | Null  
Returns `PredefinedType` if defined.

---

#### 4.3.6 ifc.is_a(typeName: Text) → Bool  
Returns `true` if the entity is of the given type or subtype.

---

### 4.4 Property Set Functions (`pset`)

#### 4.4.1 pset.has(psetName: Text) → Bool  
Checks existence of a property set.

---

#### 4.4.2 pset.get(psetName: Text, propName: Text) → Any | Null  
Returns a property value.

---

#### 4.4.3 pset.text(psetName: Text, propName: Text) → Text | Null  
Returns property value normalized as text.

---

#### 4.4.4 pset.numeric(psetName: Text, propName: Text) → Float | Null  
Returns property value normalized as numeric.

---

#### 4.4.5 pset.list() → List[Text]  
Returns all property set names present on the node.

---

### 4.5 Quantity Functions (`qto`)

#### 4.5.1 qto.get(qtoName: Text, quantityName: Text) → Float | Null  
Returns a quantity value.

---

#### 4.5.2 qto.net_area() → Float | Null  
Returns net area using schema- and tool-aware heuristics.

---

#### 4.5.3 qto.gross_area() → Float | Null  

---

#### 4.5.4 qto.net_volume() → Float | Null  

---

### 4.6 Graph Navigation Functions

#### 4.6.1 contained_in() → Node | Null  
Returns the immediate spatial container.

---

#### 4.6.2 container_chain() → List[Node]  
Returns the full spatial containment chain.

---

#### 4.6.3 aggregates() → List[Node]  
Returns aggregated child elements.

---

#### 4.6.4 decomposes() → Node | Null  
Returns the parent aggregation node.

---

#### 4.6.5 type_of() → Node | Null  
Returns the associated `IfcTypeProduct`.

---

#### 4.6.6 instances_of_type() → List[Node]  
Returns instances of the current type node.

---

#### 4.6.7 assigned_to(kind: Text) → List[Node]  
Returns assigned groups, systems, or classifications.

---

#### 4.6.8 connects_to() → List[Node]  
Returns connected nodes (MEP topology).

---

#### 4.6.9 neighbors(depth: Int = 1) → List[Node]  
Returns neighboring nodes up to a given depth.

---

### 4.7 Graph Metrics Functions

#### 4.7.1 degree() → Int  
Returns total graph degree.

---

#### 4.7.2 path_len(to: Guid, max: Int = 6) → Int | Null  
Returns shortest path length to another node.

---

#### 4.7.3 count(xs: List[Any]) → Int  
Returns the number of elements.

---

#### 4.7.4 distinct(xs: List[Any]) → List[Any]  
Returns unique elements.

---

### 4.8 Geometry Functions (`geom`)

#### 4.8.1 geom.exists() → Bool  
Returns `true` if usable geometry exists.

---

#### 4.8.2 geom.bbox() → BBox | Null  
Returns axis-aligned bounding box.

---

#### 4.8.3 geom.centroid() → Vec[3] | Null  

---

#### 4.8.4 geom.dims() → Vec[3] | Null  

---

#### 4.8.5 geom.orientation() → Vec[3] | Null  

---

#### 4.8.6 geom.signature(n: Int = 64) → Vec[n] | Null  
Returns a compact geometric signature.

---

### 4.9 Text Functions (`text`)

#### 4.9.1 text.describe() → Text  
Returns a standardized textual description.

---

#### 4.9.2 text.normalize(x: Text) → Text  

---

#### 4.9.3 text.concat(a: Text, b: Text, …) → Text  

---

### 4.10 Synthesis Functions (`synthesize` only)

#### 4.10.1 drop_pset(psetName: Text, prob: Float)

---

#### 4.10.2 drop_feature(featureName: Text, prob: Float)

---

#### 4.10.3 jitter_bbox(pct: Float)

---

#### 4.10.4 noise_numeric(featureName: Text, sigma: Float)

---

#### 4.10.5 upsample(labelName: Text, factor: Float)

---

#### 4.10.6 downsample(labelName: Text, factor: Float)

---

### 4.11 Split Functions

#### 4.11.1 split.by_building()

---

#### 4.11.2 split.by_storey()

---

#### 4.11.3 split.by_project()

---

#### 4.11.4 split.by_hash(expr: Any)

---

### 4.12 Summary

The OpenBIM-DL function catalog provides a **minimal but expressive**
set of operations to:

- navigate IFC graphs,
- extract BIM semantics,
- generate structured features,
- simulate realistic variability,
- preserve dataset integrity.

The catalog is intentionally constrained to ensure
deterministic behavior, auditability, and reproducibility.

---
