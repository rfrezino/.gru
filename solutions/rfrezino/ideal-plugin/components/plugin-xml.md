---
tags: [intellij, plugin]
type: Configuration
title: plugin.xml
description: Plugin descriptor registering the toolbar provider and the test actions.
resource: src/main/resources/META-INF/plugin.xml
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
Plugin id `fresh.com.freshbooks.fresh`, name "Fresh", depends on `com.intellij.modules.platform`.

- Registers an `editorFloatingToolbarProvider` extension. Its implementation is written as `TestVisualizationProvide` in the XML, while the class is [TestVisualizationProvide](/components/test-visualization-provider.md).
- Action group `fresh.com.freshbooks.fresh.TestVisualizationGroup` (popup) holds the [unit, integration and acceptance actions](/components/test-type-actions.md).
- The source-code action is commented out.
