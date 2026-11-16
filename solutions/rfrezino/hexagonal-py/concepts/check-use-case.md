---
type: Process
title: Check project coherence
description: Pipeline behind hexagonal check
tags: [hexagonal-py]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---

`CheckProjectCoherenceUseCase.check()`:
1. `RawPythonFilesImporter` walks the source folder for `*.py` (honouring excluded dirs).
2. `PythonFileBuilder` turns raw files into `PythonFile` with resolved imports ([Import resolution](/concepts/import-resolution.md)).
3. `HexagonalProjectBuilder` assigns files to layers.
4. `HexagonalDependencyFlowChecker` finds violations ([rules](/concepts/layers-and-rules.md)).
5. Errors are converted to `HexagonalError` with messages for the [CLI](/concepts/cli.md).
