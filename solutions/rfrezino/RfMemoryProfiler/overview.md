---
type: "Project Overview"
title: "RfMemoryProfiler overview"
description: "Delphi unit that profiles memory allocation in real time to find leaks."
---
# RfMemoryProfiler

A Delphi unit (`uRfMemoryProfiler.pas`, by Rodrigo Farias Rezino) that watches how memory is allocated, aiming to find leaks non-intrusively at runtime.

**Install:** make it the first unit of the project (or right after a third-party memory manager's unit).

**Report:** no viewer yet; call `SaveMemoryProfileToFile` to write a text file `RfMemoryReport` next to the executable.

**Warning:** if you use the VMT `vmtAutoTable` slot, do not enable `TRACEINSTANCES`.

Four modes: by objects, by buffers, objects by creating method, buffers by allocating method.

See [object tracking](/concepts/object-tracking.md), [buffer tracking](/concepts/buffer-tracking.md), [options](/concepts/compile-options.md), [report API](/concepts/report-api.md).
