---
type: "Component"
title: "IDE Integration"
description: "Plugin entry point, keybindings, menus, editor helpers."
---
# IDE Integration

`Source/FindUnit.Main.pas` defines `TRFindUnitMain` (IOTAKeyboardBinding) with `Register`, splash/about box registration, `CreateMenus`, and handlers `AutoImport`, `OrganizeUses`, `OpenForm`, `GetUnusedUses`.

Related units: `FindUnit.FileEditor` (edits uses clauses in the active editor), `FindUnit.OTAUtils`, `FindUnit.FormSearch`/`FormSettings`/`FormMessage` (UI), `FindUnit.Settings`, and `ideInteraction/implementations/FindUnit.PaintUnusedUses.pas` which highlights unused uses.

Uses [Environment Controller](/components/environment-controller.md), [Auto Import](/components/auto-import.md), [Unused Uses](/components/unused-uses.md).
