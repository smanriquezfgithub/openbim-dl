# OpenBIM Data Language (OpenBIM-DL)

OpenBIM Data Language (OpenBIM-DL) is a declarative, graph-first language
for generating machine-learning datasets from IFC (STEP) building models.

It is designed to bridge the gap between rich openBIM semantics
and modern AI training pipelines (tabular, graph, text, 3D).

## Status
⚠️ Draft specification (v0.1)  
This repository currently contains the language specification,
not an executable implementation.

## Why OpenBIM-DL?
- IFC models are graph-structured, but most ML pipelines are not.
- Existing workflows require custom scripting and are hard to reproduce.
- OpenBIM-DL provides a formal, auditable, and reproducible way
  to transform IFC into training-ready datasets.

## What's inside
- Language Reference (normative)
- Function catalog
- Canonical examples
- Whitepaper (work in progress)

## Roadmap
- v0.1 Language specification (current)
- v0.2 Reference runtime (CLI)
- v0.3 Web execution (SaaS)
