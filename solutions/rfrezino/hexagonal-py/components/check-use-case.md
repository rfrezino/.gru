---
type: "Component"
title: "CheckProjectCoherenceUseCase"
description: "Orchestrates import, project build and flow check"
---
# Flow
1. [Import files](/components/file-import-pipeline.md) from the source folder.
2. `HexagonalProjectBuilder` assigns files to layers (index = position from innermost, starting 1) and collects `files_not_in_layers`.
3. [Flow checker](/components/dependency-flow-checker.md) finds violations.
4. Errors are converted to `HexagonalError` (message, layer names, file and module paths, group_inter_dependency).

Returns `HexagonalCheckResponse`. Used by the [CLI](/components/cli.md).
