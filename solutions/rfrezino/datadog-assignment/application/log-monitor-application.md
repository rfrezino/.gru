---
type: "Application Service"
title: "LogMonitorApplication"
description: "Main loop computing status every update_rate seconds"
---

`run()` loops: reads buckets from the last update_rate+1 seconds, computes request rate, response sizes and top-5 lists, attaches the current alert from [AlertService](/domain/alerting.md), presents via the presenter, then sleeps (default 10s).
