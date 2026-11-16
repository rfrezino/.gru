---
type: Delphi Unit
title: TDataDogStatsClientSender
description: Queues datagrams and flushes them over UDP (Indy) from a background thread.
resource: delphiDatadog.statsDClientSender.impl.pas
tags: [delphi]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-16T00:00:00Z" }
---
Interface `IDataDogStatsClientSender` has a single `Send(Content: string)`. The implementation wraps a private thread class that holds a critical-section-guarded `TStringList`. Its loop wakes every 500 ms, swaps the list, configures a `TIdUDPClient` from the [service check](/units/service-check.md) host/port, and sends each message as one datagram. The thread is free-on-terminate; the wrapper's destructor only calls Terminate, so queued messages may be lost on shutdown.

Requires Indy (IdUDPClient). Consumed by the [client](/units/statsdclient-impl.md).
