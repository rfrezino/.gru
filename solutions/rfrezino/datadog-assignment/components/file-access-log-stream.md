---
type: Component
title: FileAccessLogStream
description: Tails a file via the system tail command.
resource: src/infrastructure/log/file_access_log_stream.py
tags: [infrastructure]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Spawns `tail -n 0 -F file` and yields decoded, stripped lines; kills the process on exit. Depends on the host having tail (IMPROVEMENTS.md suggests watchdog). Implements [AccessLogStream](/components/access-log-stream.md).

