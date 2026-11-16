---
type: Component
title: Search
description: String search with caching and ranking of results
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.SearchString.pas
---

`TSearchString` matches identifiers against the [unit cache](/components/unit-cache.md); `FindUnit.SearchStringCache` (interface `Interf.SearchStringCache`) caches queries and `FindUnit.ResultsImportanceCalculator` ranks results. UI is `FindUnit.FormSearch`.
