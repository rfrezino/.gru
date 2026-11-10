---
type: "Overview"
title: "datadog-delphi overview"
description: "Delphi library that sends metrics and events to a Datadog agent over UDP using the StatsD protocol."
---
A StatsD client for Delphi (provided by Coolblue). Application code uses [IDataDogStatsClient](/components/stats-client.md), which formats StatsD lines and hands them to a [sender](/components/sender.md) that batches and ships them via UDP (Indy `TIdUDPClient`) to host/port from a [service check](/components/service-check.md) config object (default localhost:8125). See the [demo](/components/demo.md).
