---
type: "Component"
title: "Unused Uses"
description: "Detects unused units in a uses clause."
---
# Unused Uses

`TUnsedUsesProcessor` (`Source/FindUnit.UnusedUses.pas`) parses a file, collects used types, units in uses, ignored types/uses and optional prefixes, and computes unused ones (`uetUnused`, `uetNoPasFile`); `GetUnusedUsesAsString` reports. Needs the [Environment Controller](/components/environment-controller.md) to resolve units. Experimental; must be enabled in settings. Tests in `Test/TestUnsedUses.pas`.
