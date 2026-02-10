# OpenBIM Data Language (OpenBIM-DL)
## A Declarative Language for Reproducible AI Dataset Generation from IFC Models

### Whitepaper — Version 0.1 (Revised)

---

## Abstract

Industry Foundation Classes (IFC) provide a rich, standardized representation of built assets,
capturing geometry, semantics, relationships, and domain knowledge across the building lifecycle.
However, leveraging IFC data for modern Artificial Intelligence (AI) workflows remains challenging.

Current approaches rely on ad-hoc scripting, ETL pipelines, or generic data technologies that were
not designed for IFC’s graph-native, heterogeneous structure. These approaches often lead to
non-reproducible pipelines, implicit assumptions, and fragile transformations.

This paper introduces **OpenBIM Data Language (OpenBIM-DL)**, a declarative domain-specific language
designed to formally specify the extraction, transformation, and synthesis of AI-ready datasets
from IFC models. OpenBIM-DL defines a reproducible contract between BIM semantics and AI pipelines,
supporting tabular, graph-based, and text-based learning workflows.

---

## 1. Motivation

### 1.1 IFC as a Data Source for AI

IFC is fundamentally:
- a **typed object graph**,
- with explicit relationships,
- heterogeneous attributes and property sets,
- optional and schema-dependent information,
- mixed geometric and semantic content.

These characteristics make IFC expressive and interoperable, but also difficult to integrate
directly into AI workflows, which typically expect:
- flat tables,
- homogeneous feature vectors,
- explicit labels,
- reproducible preprocessing.

Bridging this gap currently requires substantial manual effort.

---

### 1.2 Limitations of Current Practices

In practice, IFC-to-AI pipelines are typically implemented using:
- custom scripts (Python, C#, Java),
- ETL tools,
- ad-hoc graph exports,
- partial RDF conversions.

These approaches share common limitations:

- **Implicit logic**  
  Transformation rules are embedded in code, not formally documented.

- **Low reproducibility**  
  Minor code or library changes may alter results.

- **Poor auditability**  
  It is difficult to trace how a specific dataset was produced.

- **Tight coupling to execution**  
  The *definition* of the dataset is inseparable from *how* it is computed.

These limitations hinder scientific reproducibility and industrial scalability.

---

### 1.3 Design Goal

OpenBIM-DL is motivated by a simple but strict goal:

> Provide a **formal, declarative, and reproducible specification** for generating AI datasets from IFC models, independent of execution technology.

---

## 2. Comparison with Existing Approaches

This section compares OpenBIM-DL with commonly used approaches, focusing on **capability boundaries**, not superiority claims.

---

### 2.1 IFC Scripting (Python, IfcOpenShell, C#, Java)

**Description**  
Custom scripts directly traverse IFC data structures, extract values, and build datasets.

**Strengths**
- Maximum flexibility  
- Full access to IFC internals  
- Mature tooling (e.g., IfcOpenShell)

**Limitations**
- Transformation logic is implicit and scattered  
- Reproducibility depends on code, environment, and libraries  
- Difficult to audit or standardize across teams  
- Dataset definition is inseparable from execution code  

**Relation to OpenBIM-DL**  
OpenBIM-DL does **not replace scripting**.  
Instead, it defines a *formal specification layer* that scripting-based runtimes may implement.

---

### 2.2 ETL Pipelines (Extract–Transform–Load)

**Description**  
ETL tools move data from IFC into relational or analytical stores.

**Strengths**
- Well-established for structured data  
- Scalable execution engines  
- Clear separation of pipeline stages  

**Limitations**
- IFC semantics must be flattened early  
- Graph relationships are often lost or denormalized  
- ETL tools lack native understanding of IFC constructs  
- Transformations are procedural, not semantic  

**Relation to OpenBIM-DL**  
OpenBIM-DL complements ETL by:
- preserving graph semantics during transformation,  
- defining IFC-aware derivation rules,  
- producing outputs consumable by ETL systems.

---

### 2.3 RDF / Linked Data (IFCOWL, SPARQL)

**Description**  
IFC is mapped to RDF/OWL and queried using SPARQL.

**Strengths**
- Formal semantics  
- Explicit graph structure  
- Strong reasoning capabilities  
- Interoperability with knowledge graphs  

**Limitations**
- High complexity for practitioners  
- Performance overhead for large models  
- Not optimized for ML feature engineering  
- SPARQL queries are often verbose for numeric feature extraction  

**Relation to OpenBIM-DL**  
OpenBIM-DL is **not a semantic replacement for RDF**.  
It is a **feature engineering language**, not an ontology language.

RDF excels at reasoning and integration; OpenBIM-DL focuses on reproducible dataset generation.

---

### 2.4 SQL and Relational Models

**Description**  
IFC data is transformed into relational schemas and queried using SQL.

**Strengths**
- Mature query language  
- Efficient aggregation  
- Broad ecosystem  

**Limitations**
- Graph relationships require complex joins  
- Schema design decisions bias downstream analysis  
- Geometry handling is non-native  
- IFC heterogeneity is difficult to model relationally  

**Relation to OpenBIM-DL**  
OpenBIM-DL operates **before** SQL:
- it defines *what* data should exist,  
- SQL operates on the resulting dataset.

---

## 3. OpenBIM-DL Approach

### 3.1 Declarative Specification

OpenBIM-DL separates:
- **dataset definition** (language),  
- **dataset execution** (runtime).

This separation enables:
- reproducibility,  
- auditability,  
- multiple compatible runtimes.

---

### 3.2 Graph-First Model

OpenBIM-DL treats IFC as:
- a semantic graph,  
- with nodes (entities),  
- edges (relationships),  
- optional geometry.

Graph navigation is explicit and first-class.

---

### 3.3 Explicit Handling of Imperfect Data

Real IFC data is incomplete and inconsistent.

OpenBIM-DL:
- represents missing data explicitly as `Null`,  
- forbids implicit defaulting,  
- treats validation results as data.

This enables AI models to learn from *data quality itself*.

---

### 3.4 Dataset Projections

OpenBIM-DL does not define a single dataset format.  
Instead, it defines **dataset projections**: deterministic transformations of an IFC graph into
AI-oriented data representations.

A dataset projection is defined by:
- a **view** (selection of IFC entities),  
- a **derive step** (feature extraction and transformation),  
- an optional **synthesize step**,  
- and a **target representation**.

The primary dataset projections supported by OpenBIM-DL are:

#### a) Tabular Projection
- Rows represent IFC entities.  
- Columns represent derived features.  
- Suitable for classical machine learning, quality assurance, and analytics.

#### b) Text Projection
- Each entity is mapped to a controlled textual description.  
- Text is treated as a **first-class feature**, not documentation.  
- Suitable for embeddings, semantic search, and retrieval-augmented generation.

#### c) Graph Projection
- Nodes represent IFC entities.  
- Edges represent explicit IFC relationships.  
- Suitable for graph algorithms and graph neural network training.

These projections are **complementary views of the same IFC source**, enabling multiple AI workflows
to operate on a shared, reproducible semantic foundation.

---

## 4. AI Use Cases

### 4.1 Tabular Machine Learning

**Use cases**
- classification of elements,  
- regression on quantities,  
- anomaly detection.

**OpenBIM-DL contribution**
- deterministic feature derivation,  
- explicit missingness,  
- reproducible preprocessing.

Result: structured tables suitable for classical ML and deep learning.

---

### 4.2 Graph Neural Networks (GNN)

**Use cases**
- MEP system inference,  
- connectivity-based classification,  
- topological anomaly detection.

**OpenBIM-DL contribution**
- controlled graph projection,  
- explicit node and edge features,  
- stable topology definitions.

Result: consistent graph datasets without ad-hoc extraction code.

---

### 4.3 Text Embeddings and Semantic Search

**Use cases**
- semantic similarity,  
- retrieval-augmented generation,  
- model explainability.

**OpenBIM-DL contribution**
- standardized textual descriptions derived from BIM semantics,  
- controlled mapping from features to text,  
- reproducible embedding inputs aligned with IFC identifiers.

Result: text corpora that can be directly consumed by modern embedding models, enabling semantic
search and AI agents grounded in BIM data.

---

## 5. Scope and Limitations

OpenBIM-DL intentionally does **not** aim to:
- replace IFC authoring tools,  
- replace IFCOWL or semantic reasoning,  
- define execution engines or storage systems.

It focuses strictly on **dataset specification and projection**.

---

## 6. Conclusion

OpenBIM-DL addresses a critical gap between BIM interoperability standards and AI workflows.

By formalizing dataset generation as a declarative language, it enables:
- reproducible research,  
- auditable industrial pipelines,  
- consistent integration of BIM semantics into AI systems.

OpenBIM-DL does not compete with existing tools; it provides a missing specification layer
that allows them to interoperate more reliably.

---

## References

- buildingSMART International. *Industry Foundation Classes (IFC)*.  
- Eastman et al. *BIM Handbook*.  
- ISO 16739.  
- ISO 19650.  
- W3C. *RDF and SPARQL Specifications*.  
- Kipf & Welling. *Graph Convolutional Networks*.  
