---
type: Component
title: Plugin entry
description: TRFindUnitMain keyboard binding registering IDE shortcuts
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.Main.pas
---

`TRFindUnitMain` implements `IOTAKeyboardBinding` and binds the search, organize-uses and auto-import shortcuts. Also see `FindUnit.CompilerInterceptor` (`TCompilerInterceptor`, an `IOTAIDENotifier` that observes compiles) and `FindUnit.FormSettings`/`FindUnit.Settings` for options.

Delegates to the [environment controller](/components/environment-controller.md). Part of the [overview](/overview.md).
