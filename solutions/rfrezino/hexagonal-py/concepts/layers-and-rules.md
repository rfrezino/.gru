---
type: Rules
title: Layers and dependency rules
description: How layer order and directory groups define allowed imports
tags: [hexagonal-py]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---

Layers are ordered; the innermost (e.g. domain) may not import any outer layer. Outer layers may import inner ones.

Two error kinds from `HexagonalDependencyFlowChecker`:
- Wrong flow: a file in an inner layer imports a file in an outer layer (`inner.index < outer.index`).
- Group inter-dependency: within the same layer, a file imports a file from a different directory group (e.g. MySql vs Postgres adapters).

Imports that resolve to files outside every layer are ignored. Files not in any layer are listed as warnings in the check output.

Domain classes: `HexagonalLayer`, `HexagonalProject`, `HexagonalProjectLayer`, `PythonFile`, `RawPythonFile`, `HexagonalError`.
Test fixtures under `tests/integration_tests/utils/sample_projects` hold correct and wrong clean-arch and hexagonal-arch projects.
