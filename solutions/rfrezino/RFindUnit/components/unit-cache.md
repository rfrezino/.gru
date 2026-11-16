---
type: Component
title: Unit cache
description: TUnits/TUnitsController persistent cache of parsed units
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.FileCache.pas
---

`TUnitsController` and `TUnits` keep parsed unit data in memory and persist it to disk (`FindUnit.FileEditor`, `FindUnit.StringPositionList` support editing and positions). Queried by [search](/components/search.md) and filled by the [parser worker](/components/parser-worker.md).
