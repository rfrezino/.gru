---
type: "Component"
title: "Units Cache"
description: "In-memory index of parsed units and search."
---
# Units Cache

`Source/FindUnit.FileCache.pas`: `TUnits` is a dictionary of path to `TPasFile`; `TUnitsController` answers `GetFindInfo` (partial) and `GetFindInfoFullMatch` searches and gets/extracts a `TPasFile`. Related: `FindUnit.SearchString`, `FindUnit.SearchStringCache` (`Interf.SearchStringCache`), `FindUnit.ResultsImportanceCalculator` (result ranking), `FindUnit.StringPositionList`.
