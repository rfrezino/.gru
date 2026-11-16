---
tags: [intellij, plugin]
type: Class
title: AnTestVisualizationAction
description: Abstract base action that maps a source file to its test file, creates and opens it.
resource: src/main/kotlin/fresh/com/freshbooks/fresh/AnTestVisualizationAction.kt
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
Constructor takes a `testType` and create/open icons.

- Test path: `<project>/tests/<testType>/<relative path>/test_<file>`; the first path segment of the relative path (e.g. `src`) is dropped.
- `openTestFile`: creates missing directories and the file (content from the new-file template if any), refreshes the VFS, opens it in the editor.
- `update`: visible only for `.py` source files outside `tests/unit|integration|acceptance`; text is "Open <type> Test" if the test exists, else "Create <type> Test".
- `getSourceCodeFile` strips `test_` and searches the project root directory listing (cached; the cache check is date based, so effectively refreshed daily).
- `isTestFile` detects test files by path or `test_` prefix.

Subclasses: [test type actions](/components/test-type-actions.md), [VisualizeSourceCodeAction](/components/visualize-source-code-action.md).
