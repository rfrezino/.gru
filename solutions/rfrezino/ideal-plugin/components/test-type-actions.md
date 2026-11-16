---
tags: [intellij, plugin]
type: Class
title: Unit, Integration and Acceptance actions
description: Three thin subclasses that open or create the test file of their type.
resource: src/main/kotlin/fresh/com/freshbooks/fresh/VisualizeUnitTestAction.kt
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
| Class | testType | Action id |
|---|---|---|
| VisualizeUnitTestAction | unit | PyFile.Tests.UnitTests |
| VisualizeIntegrationTestAction | integration | PyFile.Tests.IntegrationTests |
| VisualizeAcceptanceTestAction | acceptance | PyFile.Tests.AcceptanceTests |

Each calls `openTestFile` from [AnTestVisualizationAction](/components/an-test-visualization-action.md). Files: VisualizeUnitTestAction.kt, VisualizeIntegrationTestAction.kt, VisualizeAcceptanceTestAction.kt.
