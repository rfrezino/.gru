---
type: "Mechanism"
title: "Buffer tracking"
description: "How GetMem/FreeMem/ReallocMem/AllocMem are wrapped to count non-object buffers by size."
---
# Buffer tracking

- Counters live in an integer array indexed by buffer size, `0..SIZE_OF_MAP` (65365); larger sizes use the last slot.
- Each allocation is enlarged by a header (`TMappedRecord`, size `GAP_SIZE`): a parity value marking buffers created by the profiler, the address of the size counter, and (with `BUFFER_TRACKER`) the caller's code address.
- The returned pointer is advanced past the header. `FreeMem` steps back, checks parity, decrements the counter and frees. Without a size parameter in `FreeMem`, this avoids a locked pointer-to-size list.
- Original manager routines are kept in `SDefaultGetMem`, `SDefaultFreeMem`, `SDefaultReallocMem`, `SDefaultAllocMem`.
- Report shows buffer size, number of allocations, total bytes.
