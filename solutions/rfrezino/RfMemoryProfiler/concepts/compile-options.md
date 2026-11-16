---
type: Configuration
title: Compile-time options
description: Conditional defines in RfMemoryProfilerOptions.Inc.
tags: [config]
generated: { by: "claude-code/sonnet-5.5", at: "2026-11-16T00:00:00Z" }
sources:
  - { id: inc, resource: "RfMemoryProfilerOptions.Inc", title: "Options include" }
---
# Defines (all on by default)
| Define | Effect |
|---|---|
| INSTANCES_COUNTER | Count all objects. |
| BUFFER_COUNTER | Count non-object buffers grouped by size. |
| INSTANCES_TRACKER | Also group objects by caller method; requires INSTANCES_COUNTER; TObject itself not counted. |
| BUFFER_TRACKER | Also identify the caller method of buffer allocations. |

Used by [object tracking](/concepts/object-tracking.md) and [buffer tracking](/concepts/buffer-tracking.md).
