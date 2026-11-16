---
type: API
title: Public interface of uRfMemoryProfiler
description: Exported routines of the profiler unit.
tags: [api]
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
sources:
  - { id: src, resource: "uRfMemoryProfiler.pas", title: "Main unit" }
  - { id: readme, resource: "README.md", title: "README" }
---
# Interface
| Routine | Purpose |
|---|---|
| `SaveMemoryProfileToFile(AFilePath: string = '')` | Write the text report (default RfMemoryReport next to the exe). |
| `RfGetInstanceList: TList` | List of tracked class controllers. |
| `InitializeRfMemoryProfiler` | Install the hooks. |

Test-visible helpers (under UNITTEST): `GetAmountOfBufferAllocations`, `GetAmountOfAllocationOfClass`, `GetBytesAmountOfInstanceAllocation`, `GetBytesAmountOfBufferAllocation`, `GetBytesAmountOfUsedMemory`.

See [overview](/concepts/overview.md).
