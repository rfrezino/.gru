---
type: Feature
title: Reusable themes and languages (spec 004)
description: Shared theme catalog with a custom colour wheel picker; entities reference a theme by id and resolve it at read time.
tags: [feature, themes, languages]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: r4, resource: /specs/004-reusable-themes/research.md }
  - { id: s3, resource: /specs/003-settings-theme-customization/spec.md }
---

* Spec 003 replaced raw hex inputs with visual pickers, live preview, presets, recent colours and contrast warnings.[^s3]
* Spec 004 moved colours and font into top-level `themes/{id}`; entities hold `themeId` plus their own `logoUrl`. Themes resolve at read time, so edits propagate with no fan-out writes, at the cost of one extra read per entity page.[^r4]
* The colour wheel is custom (canvas, HSL math); no npm dependency, which also avoids a procurement step.
* Retiring a theme: [retireTheme](/functions/retire-theme.md). A languages registry has admin CRUD with direct soft delete.
* Rules: [security rules](/data/security-rules.md).

[^r4]: Reusable themes research
[^s3]: Settings theme customization spec
