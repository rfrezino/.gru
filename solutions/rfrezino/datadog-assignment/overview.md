---
type: "Overview"
title: "HTTP log monitor overview"
description: "Console tool that tails an access log and alerts on high traffic"
---

Python 3.8 CLI (`src/bin/log-monitor`, click) that tails an HTTP access log in Common Log Format, aggregates it into per-second buckets and prints stats every 10s, plus a high-traffic alert.

Options: `-l/--log-file` (default /tmp/access.log), `-w/--alert-window` (120s), `-t/--alert-threshold` (10 req/s).

Layers (hexagonal): [domain](/domain/log-model.md), [application](/application/log-monitor-application.md), [infrastructure](/infrastructure/adapters.md). A reader process feeds a shared repository; the main process polls it. See [reader](/application/access-log-reader-process.md).

Docker: `docker-compose.yml` runs an nginx webserver, the monitor and a k6 load test (`docker/`).
