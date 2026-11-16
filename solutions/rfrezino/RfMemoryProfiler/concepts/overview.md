---
type: Project
title: RfMemoryProfiler overview
description: Delphi unit that profiles memory allocation in real time to help find leaks.
tags: [delphi, memory, profiler]
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
sources:
  - { id: src, resource: "uRfMemoryProfiler.pas", title: "Main unit" }
  - { id: readme, resource: "README.md", title: "README" }
---
# Overview
A non-intrusive Delphi unit that watches how memory is allocated, aimed at finding leaks at run time.

- Install by placing `uRfMemoryProfiler` as the first unit of the project (just after a third-party memory manager unit, if any).
- No viewer exists; call `SaveMemoryProfileToFile` to write a text report named RfMemoryReport in the executable path.
- Warning: if the VMT slot `vmtAutoTable` is used by the application, do not enable the INSTANCES_TRACKER-style tracing directive (TRACEINSTANCES).
- Two approaches: [object tracking](/concepts/object-tracking.md) and [buffer tracking](/concepts/buffer-tracking.md), each optionally with caller-method identification ([options](/concepts/compile-options.md)).

Files: `uRfMemoryProfiler.pas` (implementation), `RfMemoryProfilerOptions.Inc`, `Documentation.txt`, and [tests](/concepts/unit-tests.md). Author: Rodrigo Farias Rezino.
