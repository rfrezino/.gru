---
type: Delphi Unit
title: IDataDogStatsClient and TDataDogStatsClientImpl
description: Public client interface and its implementation that formats metric lines and forwards them to a sender.
resource: delphiDatadog.statsDClient.impl.pas
tags: [api]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-16T00:00:00Z" }
---
Interface in `delphiDatadog.statsDClient.interf.pas`; implementation `TDataDogStatsClientImpl` takes an [`IDataDogStatsClientSender`](/units/statsdclient-sender.md) and has a Prefix prepended to every aspect.

| Method family | Wire type | Notes |
|---|---|---|
| Count, IncrementCounter/Increment, DecrementCounter/Decrement | `c` | increments delegate to Count with +1/-1 |
| RecordGaugeValue / Gauge (Double, Int64) | `g` | |
| RecordExecutionTime / Time | `ms` | Int64 milliseconds |
| RecordHistogramValue / Histogram (Double, Int64) | `h` | |
| RecordSetValue | `s` | string value |
| RecordEvent | event | takes [TDataDogEvent](/units/event.md) |

Line format: `prefix+aspect:value|type[|samplerate]` plus tags from [utils](/units/utils.md). Doubles are formatted with an invariant FormatSettings.

Known quirks: sample-rate overloads call `IsInvalidSample` with inconsistent polarity (Count and Int64 gauge exit when the check is false, the histogram ones when true), and the Double gauge sample overload does no validation. Prefix is not followed by a separator automatically.
