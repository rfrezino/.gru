---
type: Feature
title: AI-assisted content translation (spec 005)
description: Per-language tabbed editing for members, news and daily messages with AI translate and a reviewed marker.
tags: [feature, translation, admin]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: r5, resource: /specs/005-content-translation/research.md }
---

* One shared mechanism: `useContentTranslation` hook plus `LanguageTabs`; each form keeps its own field list. A generic schema-driven form was rejected as over-abstraction for three content types.[^r5]
* `translationStatus: Record<lang, "reviewed">` lives on the item. Presence means that language was saved at least once; the "AI-generated, unreviewed" state is transient form state only.
* Overwriting a reviewed language asks `window.confirm`, matching the soft-delete confirms.
* Translation runs server-side via [translateContentFields](/functions/translate-content-fields.md); content carries `primaryLanguage`.

[^r5]: Content translation research
