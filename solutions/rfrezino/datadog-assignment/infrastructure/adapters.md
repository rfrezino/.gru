---
type: "Adapters"
title: "Infrastructure adapters"
description: "File stream, CLI presenter and signal handling"
---

- `FileAccessLogStream` (`src/infrastructure/log/file_access_log_stream.py`): implements the stream port over a file.
- `CliLogMonitorPresenter`: prints [status](/domain/alerting.md) to the console.
- `signal_handling`: `register_signals` raises `SignalException` on SIGTERM/SIGINT so the CLI can terminate the reader.
