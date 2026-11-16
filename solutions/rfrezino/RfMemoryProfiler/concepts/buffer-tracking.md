---
type: Mechanism
title: Memory buffer tracking
description: How non-object allocations are counted by size via a hooked memory manager.
tags: [memory-manager, buffers]
sources:
  - { id: doc, resource: "Documentation.txt", title: "Memory Allocation Control" }
  - { id: src, resource: "uRfMemoryProfiler.pas", title: "Main unit" }
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
---
# How it works
- GetMem, FreeMem, ReallocMem and AllocMem are replaced with wrappers.
- An integer array indexed 0..65365 (`SIZE_OF_MAP`) counts allocations per size; sizes at or above the limit share the last slot.
- FreeMem receives only a pointer, so each allocation is enlarged by a small header (`TMappedRecord`) holding a parity marker (to know whether the profiler allocated it), the address of the size-map slot, and the caller frame address. Free reads the header to decrement the right counter.
- Report rows: buffer size, number of allocations, total bytes.
- With BUFFER_TRACKER the caller address is also recorded.

Related: [object tracking](/concepts/object-tracking.md), [options](/concepts/compile-options.md).
