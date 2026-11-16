---
type: Component
title: CLI entrypoint (log-monitor)
description: click command wiring all components together.
resource: src/bin/log-monitor
tags: [cli]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Options:
| Option | Default | Meaning |
|---|---|---|
| -l/--log-file | /tmp/access.log | file to monitor, must exist |
| -w/--alert-window | 120 | alert window in seconds |
| -t/--alert-threshold | 10 | req/s threshold |

Builds [FileAccessLogStream](/components/file-access-log-stream.md), a [repository](/components/log-data-bucket-repository.md) keeping window*2 buckets, an [AlertService](/components/alert-service.md), starts [AccessLogReaderProcess](/components/access-log-reader-process.md), registers SIGTERM/SIGINT (raising SignalException from src/infrastructure/signal_handling.py) and runs [LogMonitorApplication](/components/log-monitor-application.md); on a signal the reader is terminated.

