---
type: Component
title: Auto import
description: TAutoImport adds missing units to uses clauses
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.AutoImport.pas
---

`TAutoImport` resolves the identifier under the cursor via [search](/components/search.md) and inserts the unit in the interface or implementation `uses` clause using `FindUnit.FileEditor`.
