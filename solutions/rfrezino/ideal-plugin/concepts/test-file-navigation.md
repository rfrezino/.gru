---
type: "Class"
title: "AnTestVisualizationAction"
description: "Abstract base action mapping a Python source file to its test file and opening or creating it."
---
# Behaviour
- Constructor takes `testType` and create/open icons.
- **Path mapping:** a source file `<project>/a/b/x.py` maps to `<project>/tests/<testType>/b/test_x.py` (the first path segment after the project root is dropped).
- `openTestFile`: creates missing directories, creates the file (content from the new-file template if any), refreshes the VFS, then opens it in the editor.
- `update`: visible only for source files (`.py`, not under tests/unit, tests/integration or tests/acceptance). Text becomes "Open <type> Test" with the open icon if the test exists, else "Create <type> Test".
- `getSourceCodeFile`: for a `test_*` file, searches the project root's direct children for the matching name (the cache check compares in days, so it effectively refreshes daily).
- `isTestFile`: path in a tests/ type folder or name starts with `test_`.

Subclasses: [test actions](/concepts/test-actions.md).
