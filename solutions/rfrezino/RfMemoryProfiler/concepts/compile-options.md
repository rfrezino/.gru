---
type: "Configuration"
title: "Compile options"
description: "Conditional defines in RfMemoryProfilerOptions.Inc."
---
# Compile options

| Define | Effect |
|---|---|
| `INSTANCES_COUNTER` | Count all objects. |
| `BUFFER_COUNTER` | Count non-object buffers grouped by size. |
| `INSTANCES_TRACKER` | Group object counts by class and creating method (requires `INSTANCES_COUNTER`; TObject itself not tracked). |
| `BUFFER_TRACKER` | Group buffer counts by allocating method. |

All are defined by default. `UNITTEST` exposes internals for [tests](/concepts/unit-tests.md).
