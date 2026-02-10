# OpenBIM Data Language (OpenBIM-DL)
## Language Reference — Version 0.1

---

## Chapter 3 — Grammar

### 3.1 Overview

This chapter defines the **grammar and structural rules** of OpenBIM Data Language (OpenBIM-DL).

OpenBIM-DL is a **declarative, block-oriented language** designed to describe
*what* data should be extracted, transformed, and synthesized from IFC models,
not *how* it should be executed.

The grammar prioritizes:
- readability over compactness,
- explicit structure over implicit behavior,
- reproducibility over flexibility.

An OpenBIM-DL document describes a **data recipe**, not a program.

---

### 3.2 Document Structure

An OpenBIM-DL document consists of a sequence of **top-level blocks**.

Each block has:
- a well-defined purpose,
- a fixed execution role,
- a deterministic evaluation order.

#### 3.2.1 Canonical Block Order

The canonical order is:

1. `source`
2. `view`
3. `derive`
4. `synthesize` (optional)
5. `split` (optional)
6. `export`

Blocks must appear **at most once**, except where explicitly allowed.

---

### 3.3 Lexical Conventions

#### 3.3.1 Identifiers

Identifiers:
- are case-sensitive,
- must start with a letter or `_`,
- may contain letters, digits, and `_`.

Examples:
wall_height
gross_area
storey_id


#### 3.3.2 Literals

Supported literals:
- `Int` → `42`
- `Float` → `3.14`
- `Text` → `"IfcWall"`
- `Bool` → `true`, `false`
- `Null` → `null`

---

### 3.4 Expressions

Expressions are **side-effect free** and always evaluate to a value.

An expression may reference:
- the current node or edge context,
- built-in functions,
- previously defined symbols.

Examples:
ifc.type()
qto.net_area()
coalesce(pset.get("Pset_WallCommon","FireRating"), "Unknown")


Expressions may be nested arbitrarily.

---

### 3.5 The `source` Block

#### 3.5.1 Purpose

The `source` block defines **where the IFC data comes from** and
establishes the root model context.

#### 3.5.2 Syntax
source {
format = "IFC"
}


#### 3.5.3 Semantics

- Exactly one `source` block is required.
- The source format must be explicitly declared.
- Runtime-specific details (paths, storage, authentication)
  are intentionally outside the language scope.

---

### 3.6 The `view` Block

#### 3.6.1 Purpose

The `view` block defines **which entities** are in scope for downstream processing.

It acts as a **semantic filter** over the IFC graph.

#### 3.6.2 Syntax
view Elements {
select node
where ifc.is_a("IfcElement")
}


#### 3.6.3 Semantics

- A `view` defines a named set of nodes or edges.
- The `select` clause determines the context type (`node` or `edge`).
- The `where` clause is a boolean expression.

Multiple views may be defined.

---

### 3.7 The `derive` Block

#### 3.7.1 Purpose

The `derive` block defines **features** derived from the current context.

Derived features are:
- deterministic,
- side-effect free,
- reusable across exports.

#### 3.7.2 Syntax
derive {
height = geom.dims()[2]
area = qto.net_area()
storey = contained_in().guid()
}

#### 3.7.3 Semantics

- Each assignment defines a named feature.
- Feature names must be unique within the block.
- Evaluation occurs once per context item.

Derived values may be `Null`.

---

### 3.8 The `synthesize` Block (Optional)

#### 3.8.1 Purpose

The `synthesize` block applies **controlled perturbations**
to simulate realistic data variability.

This block is optional and non-destructive by design.

#### 3.8.2 Syntax
synthesize {
drop_pset("Pset_WallCommon", 0.2)
jitter_bbox(0.05)
}

#### 3.8.3 Semantics

- Operations are declarative, not procedural.
- Synthesis does not change original IFC data.
- Randomness must be seedable and reproducible.

---

### 3.9 The `split` Block (Optional)

#### 3.9.1 Purpose

The `split` block defines **dataset partitioning rules**
to prevent information leakage.

#### 3.9.2 Syntax
split by building
or
split by hash_group(container_chain())

#### 3.9.3 Semantics

- Splitting occurs after derivation and synthesis.
- All rows belonging to the same group are kept together.
- The split strategy must be explicit.

---

### 3.10 The `export` Block

#### 3.10.1 Purpose

The `export` block defines **output formats and schemas**.

#### 3.10.2 Syntax
export tabular {
format = "parquet"
}

or
export graph {
format = "edge_list"
}

#### 3.10.3 Semantics

- Multiple export blocks are allowed.
- Each export consumes the same derived dataset.
- Export configuration is declarative and deterministic.

---

### 3.11 Comments and Whitespace

- Line comments start with `#`
- Whitespace is insignificant except as a separator.
- Indentation is recommended but not semantically meaningful.

---

### 3.12 Summary

The OpenBIM-DL grammar defines a **clear separation of concerns**:
- `source` defines data origin,
- `view` defines scope,
- `derive` defines features,
- `synthesize` defines variability,
- `split` defines data integrity,
- `export` defines outputs.

This structure enables readable, auditable,
and reproducible transformations from IFC models
to AI-ready datasets.

---

## 3.13 Formal Grammar (EBNF)

This section defines the **formal grammar** of OpenBIM Data Language (OpenBIM-DL)
using **Extended Backus–Naur Form (EBNF)**.

The grammar specifies the **syntactic structure only**.
Semantic validation is defined in later chapters.

---

### 3.13.1 Lexical Elements

letter        ::= "A"…"Z" | "a"…"z"
digit         ::= "0"…"9"
underscore    ::= "_"

identifier    ::= ( letter | underscore ) { letter | digit | underscore }

integer       ::= digit { digit }
float         ::= digit { digit } "." digit { digit }
number        ::= integer | float

string        ::= "\"" { character } "\""
character     ::= ? any Unicode character except " and newline ?

boolean       ::= "true" | "false"
null_literal  ::= "null"

whitespace    ::= { " " | "\t" | "\n" | "\r" }

comment       ::= "#" { character } newline

---

### 3.13.2 Literals

literal ::= number
          | string
          | boolean
          | null_literal

---

### 3.13.3 Document Structure

document ::=
    whitespace
    source_block
    view_block*
    derive_block
    synthesize_block?
    split_block?
    export_block+
    whitespace

---

### 3.13.4 Source Block

source_block ::=
    "source" whitespace
    "{" whitespace
    source_param*
    "}"

source_param ::=
    identifier whitespace "=" whitespace literal

---

### 3.13.5 View Block

view_block ::=
    "view" whitespace identifier whitespace
    "{" whitespace
    select_clause
    where_clause?
    "}"

select_clause ::=
    "select" whitespace ( "node" | "edge" )

where_clause ::=
    "where" whitespace expression

---

### 3.13.6 Derive Block

derive_block ::=
    "derive" whitespace
    "{" whitespace
    derive_statement*
    "}"

derive_statement ::=
    identifier whitespace "=" whitespace expression

---

### 3.13.7 Synthesize Block

synthesize_block ::=
    "synthesize" whitespace
    "{" whitespace
    synth_statement*
    "}"

synth_statement ::=
    function_call

---

### 3.13.8 Split Block

split_block ::=
    "split" whitespace "by" whitespace split_expr

split_expr ::=
    "building"
  | "storey"
  | "project"
  | function_call

---

### 3.13.9 Export Block

export_block ::=
    "export" whitespace export_kind whitespace
    "{" whitespace
    export_param*
    "}"

export_kind ::= "tabular" | "graph"

export_param ::=
    identifier whitespace "=" whitespace literal

---

### 3.13.10 Expressions

expression ::=
    literal
  | identifier
  | function_call
  | expression "[" integer "]"

---

### 3.13.11 Function Calls

function_call ::=
    qualified_name
    "(" argument_list? ")"

qualified_name ::=
    identifier { "." identifier }

argument_list ::=
    expression { "," whitespace expression }

---

### 3.13.12 Type Annotations (Optional)

Type annotations may appear in documentation and tooling,
but are **not required syntactically** in OpenBIM-DL v0.1.

When present, they follow this form:

type_annotation ::=
    ":" whitespace type_name

type_name ::=
    "Bool"
  | "Int"
  | "Float"
  | "Text"
  | "Guid"
  | "Enum"
  | "List" "[" type_name "]"
  | "Vec" "[" integer "]"

---

### 3.13.13 Whitespace and Comments

- Whitespace may appear between any tokens.
- Comments start with `#` and extend to the end of the line.
- Comments and whitespace are ignored by the parser.

---

### 3.13.14 Grammar Notes

- Block order is enforced semantically, not syntactically.
- Expressions are side-effect free.
- Operators beyond indexing (`[]`) are intentionally excluded in v0.1.
- All identifiers are case-sensitive.

---

