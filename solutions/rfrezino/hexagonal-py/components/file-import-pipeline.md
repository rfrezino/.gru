---
type: "Component"
title: "Python file import pipeline"
description: "Discovers .py files and resolves their imports"
---
- `RawPythonFilesImporter` walks the source folder for `*.py`, skipping hidden paths and `excluded_dirs` prefixes.
- `RawPythonFileBuilder`/`PythonFileBuilder` build `RawPythonFile`/`PythonFile` domain objects.
- `PythonFileImportsResolver` uses `modulefinder.ModuleFinder` and maps unresolved (bad) modules to project files to list imported file paths.

Feeds the [check use case](/components/check-use-case.md).
