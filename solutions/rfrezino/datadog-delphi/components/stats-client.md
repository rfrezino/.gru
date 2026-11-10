---
type: "Component"
title: "Stats client (IDataDogStatsClient)"
description: "Public API and implementation that formats metrics into StatsD lines."
---
Files: `delphiDatadog.statsDClient.interf.pas` (interface) and `delphiDatadog.statsDClient.impl.pas` (`TDataDogStatsClientImpl`, constructed with an [IDataDogStatsClientSender](/components/sender.md)).

Methods, each with an optional `SampleRate` overload and `Tags: TDataDogTags`:
| Methods | StatsD type |
|---|---|
| Count, Increment(Counter), Decrement(Counter) | `c` |
| Gauge / RecordGaugeValue (Double, Int64) | `g` |
| Time / RecordExecutionTime (ms) | `ms` |
| Histogram / RecordHistogramValue (Double, Int64) | `h` |
| RecordSetValue | `s` |
| RecordEvent([TDataDogEvent](/components/event.md)) | `_e{titleLen,textLen}:...` |

Lines look like `prefix+aspect:value|type[|sampleRate][|#tags]`. `Prefix` is prepended to aspects (and event titles). Sampled overloads skip sending when `IsInvalidSample` says so. Floats are formatted with a fixed invariant format setting. Tags use [utils](/components/utils.md).
