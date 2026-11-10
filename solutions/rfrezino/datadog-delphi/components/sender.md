---
type: "Component"
title: "Sender (IDataDogStatsClientSender)"
description: "Background thread that buffers StatsD lines and sends them over UDP."
---
`TDataDogStatsClientSender` implements `IDataDogStatsClientSender.Send(Content)`. It owns a free-on-terminate `TThread` that queues messages in a `TStringList` guarded by a `TCriticalSection`. Every 500 ms the thread swaps out the queue and sends each message with `TIdUDPClient`, configuring host and port from a [TDataDogServiceCheck](/components/service-check.md). Destroying the sender terminates the thread. Used by the [stats client](/components/stats-client.md).
