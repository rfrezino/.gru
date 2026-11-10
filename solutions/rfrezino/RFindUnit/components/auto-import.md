---
type: "Component"
title: "Auto Import"
description: "Imports a unit for an undeclared identifier."
---
# Auto Import

`TAutoImport` (`Source/FindUnit.AutoImport.pas`) reads compiler "undeclared identifier" messages (see `FindUnit.CompilerInterceptor`) to build a list of classes/units to import, and memorizes the user-chosen unit per class in an INI file (`GetMemorizedUnit`, `SetMemorizedUnit`). Triggered from [IDE Integration](/components/ide-integration.md).
