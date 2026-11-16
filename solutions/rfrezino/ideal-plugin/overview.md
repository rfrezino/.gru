---
tags: [intellij, plugin]
type: Project
title: Fresh IntelliJ plugin
description: IntelliJ plugin that locates, creates and opens Python test files for a source file.
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
Fresh adds a floating editor toolbar to IntelliJ. For a Python source file it offers Unit, Integration and Acceptance test actions that open the matching test file under `tests/<type>/`, creating it (as `test_<name>.py`) when missing.

- [Plugin registration](/components/plugin-xml.md)
- [Base action](/components/an-test-visualization-action.md)
- [Toolbar provider](/components/test-visualization-provider.md)
- [Build](/build/gradle-build.md)
