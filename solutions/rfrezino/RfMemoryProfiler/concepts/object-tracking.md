---
type: Mechanism
title: Object instance tracking
description: How class instances are counted by patching TObject.NewInstance and the VMT free slot.
tags: [vmt, objects]
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
sources:
  - { id: src, resource: "uRfMemoryProfiler.pas", title: "Main unit" }
  - { id: readme, resource: "README.md", title: "README" }
---
# How it works
- `TObject.NewInstance` is replaced by a hook (`TObjectHack.NNewInstanceTrace`); each creation increments a per-class counter.
- The VMT `vmtFreeInstance` entry is redirected to `TRfObjectHack.NFreeInstance`, which decrements the counter then calls the original destructor path.
- Per-class state is held in `TRfClassController`; classes are registered via `RegisterRfClassController`.
- With INSTANCES_TRACKER, counts are grouped by class and caller address using `TAllocationMap` (sorted array with binary search, `FindOrAdd`, `IncCounter`/`DecCounter`).
- Code patching uses helpers such as `AddressPatch` and `PatchCodeDWORD`; caller addresses come from frame-based stack tracing (`GetFrameBasedStackTrace`).

See [options](/concepts/compile-options.md) and [API](/concepts/public-api.md).
