---
type: Process
title: Import resolution
description: How a file's imported modules are found
tags: [hexagonal-py]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---

`PythonFileImportsResolver` runs `modulefinder.ModuleFinder` on the file with an empty path, collects module file paths, removes the file itself, and maps unresolved ("bad") modules to project files by progressively stripping leading package segments until a file exists under the project folder. Paths are normalised. Raises if the source file does not exist. Feeds the [check use case](/concepts/check-use-case.md).
