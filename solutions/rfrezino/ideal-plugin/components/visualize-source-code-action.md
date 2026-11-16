---
tags: [intellij, plugin]
type: Class
title: VisualizeSourceCodeAction
description: Action that jumps from a test file back to its source file; currently not registered.
resource: src/main/kotlin/fresh/com/freshbooks/fresh/VisualizeSourceCodeAction.kt
status: draft
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
Visible only on test files whose source can be found via `getSourceCodeFile`; opens it. Its registration in [plugin.xml](/components/plugin-xml.md) is commented out. Extends [AnTestVisualizationAction](/components/an-test-visualization-action.md).
