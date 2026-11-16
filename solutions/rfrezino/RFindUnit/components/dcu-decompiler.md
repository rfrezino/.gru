---
type: Component
title: DCU decompiler
description: TDcuDecompiler reads compiled .dcu units
tags:
- rfindunit
status: stable
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:10:00Z'
sources:
- id: src
  resource: Source/FindUnit.DcuDecompiler.pas
---

`TDcuDecompiler` wraps Dcu32Int (see [third-party](/components/third-party.md)) to extract declarations from `.dcu` files for the [parser worker](/components/parser-worker.md).
