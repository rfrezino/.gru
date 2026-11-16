---
type: Component
title: Environment controller
description: Coordinates IDE state, project storage and parsing
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.EnvironmentController.pas
---

`TEnvironmentController` (interface `IRFUEnvironmentController` in `Interfaces/`) implements `IOTAProjectFileStorageNotifier`. It tracks the active project, owns the [parser worker](/components/parser-worker.md) and [unit cache](/components/unit-cache.md), and exposes search and import to the [plugin entry](/components/plugin-entry.md). `FindUnit.OTAUtils` holds Open Tools API helpers.
