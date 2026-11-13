---
type: Cloud Function
title: translateContentFields
description: Admin-only callable that translates a map of text fields via a swappable provider; currently a null provider.
tags: [functions, translation, ai]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /functions/src/translateContentFields.ts
---

* Takes whole field maps plus source and target language so a real provider can use cross-field context (`TranslationProvider.translateFields` in `functions/src/translation/provider.ts`).
* No provider is wired yet (`nullProvider.ts`); picking one is a procurement decision. The provider credential must only live in the Functions runtime, never the browser.
* Used by [content translation](/features/content-translation.md).
