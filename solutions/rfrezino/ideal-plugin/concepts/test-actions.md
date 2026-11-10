---
type: "Class Group"
title: "Test visualization actions"
description: "Concrete actions for unit, integration, acceptance tests and the source-code jump."
---
All extend [AnTestVisualizationAction](/concepts/test-file-navigation.md) and live in `src/main/kotlin/fresh/com/freshbooks/fresh/`.

| Class | testType | Registered action id |
|---|---|---|
| VisualizeUnitTestAction | unit | PyFile.Tests.UnitTests |
| VisualizeIntegrationTestAction | integration | PyFile.Tests.IntegrationTests |
| VisualizeAcceptanceTestAction | acceptance | PyFile.Tests.AcceptanceTests |
| VisualizeSourceCodeAction | Source Code | (commented out in plugin.xml) |

Each test action's `actionPerformed` calls `openTestFile`. VisualizeSourceCodeAction instead opens the source file for a test file and is only visible when that source exists; it is currently not registered.
