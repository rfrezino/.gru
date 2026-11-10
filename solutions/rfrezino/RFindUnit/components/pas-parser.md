---
type: "Component"
title: "Pas Parser"
description: "Extracts declarations from Delphi source units."
---
# Pas Parser

`TPasFileParser` (`Source/FindUnit.PasParser.pas`) uses DelphiAST to build a `TPasFile`: unit name, last modification, types (classes, records, interfaces, enums, references, subranges), methods, variables and constants. `SetIncluder` takes an `IIncludeHandler` (`FindUnit.IncluderHandlerInc`) to resolve `{$I}` includes. Tests: `Test/TestPasParserpas.pas`.
