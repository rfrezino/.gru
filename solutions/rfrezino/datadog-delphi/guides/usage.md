---
type: Guide
title: Usage and demo
description: How to wire up the client, from the VCL demo.
resource: Demo/uMain.pas
tags: [delphi]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-16T00:00:00Z" }
---
```delphi
Service := TDataDogServiceCheck.Create;           // localhost:8125
Sender  := TDataDogStatsClientSender.Create(Service);
Client  := TDataDogStatsClientImpl.Create(Sender);
Client.Count('qtt', 1, TDataDogTags.Create('mob', 'foo'));
Client.RecordExecutionTime('testBM', 120, TDataDogTags.Create('valueX'));
```

The demo (`Demo/Demo.dpr`, a VCL form with one button) also sends a [TDataDogEvent](/units/event.md). See [client](/units/statsdclient-impl.md) and [sender](/units/statsdclient-sender.md). No tests or build scripts exist in the repository.
