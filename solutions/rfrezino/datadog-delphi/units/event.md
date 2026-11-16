---
type: Delphi Unit
title: TDataDogEvent
description: Event data object that serializes its optional fields into DogStatsD event parameters.
resource: delphiDatadog.event.pas
tags: [delphi]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-16T00:00:00Z" }
---
Properties: Title, Text, MillisSinceEpoch, HostName, AggregationKey, Priority, SourcerTypeName (sic), AlertType.

`ToMap` emits only non-default fields: `|d:` (millis / 1000), `|h:`, `|k:`, `|p:`, `|t:`. SourcerTypeName is not emitted. Title and Text are not part of ToMap. Passed to `RecordEvent` on the [client](/units/statsdclient-impl.md). Uses [utils](/units/utils.md).
