---
type: "Component"
title: "TDataDogEvent"
description: "Datadog event model; ToMap renders the optional event metadata fields."
---
Properties: Title, Text, MillisSinceEpoch, HostName, AggregationKey, Priority, SourcerTypeName, AlertType (see [header types](/components/header-types.md)). `ToMap` emits `|d:` (seconds), `|h:`, `|k:`, `|p:`, `|t:` for non-empty values (SourcerTypeName is not emitted). Consumed by [RecordEvent](/components/stats-client.md).
