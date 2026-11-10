---
type: "Example"
title: "Demo VCL app"
description: "Sample form wiring the client and sending a count, timing and event."
---
`FormCreate` builds a [TDataDogServiceCheck](/components/service-check.md), a [sender](/components/sender.md), then a [stats client](/components/stats-client.md). The button sends `Count('qtt',1,...)`, `RecordExecutionTime` and a low-priority error [event](/components/event.md).
