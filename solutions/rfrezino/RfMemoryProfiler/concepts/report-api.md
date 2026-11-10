---
type: "API"
title: "Public API"
description: "Exported routines of uRfMemoryProfiler."
---
# Public API

- `SaveMemoryProfileToFile(AFilePath: string = '')` writes the usage report as text (default file `RfMemoryReport` in the executable folder).
- `RfGetInstanceList: TList` returns tracked class controllers.
- `InitializeRfMemoryProfiler` (only with `UNITTEST`).

Data comes from [object](/concepts/object-tracking.md) and [buffer](/concepts/buffer-tracking.md) tracking.
