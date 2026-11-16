---
type: Source Module
title: log_monitor
tags: [log-monitor]
description: Wires reader and processor jobs around a shared list
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/log_monitor/log_monitor.py

Frozen dataclass `LogMonitor(Active, w3c_log_file_reader_job, loglines_processor_job, logger)` and the lifecycle functions `configure_log_monitor`, `initialize_log_monitor`, `stop_log_monitor`; each returns a new immutable instance. `configure_log_monitor` creates the shared [ThreadSafeList](/components/thread-safe-list.md) and passes it as output of the [reader job](/components/w3c-log-file-reader-job.md) and input of the [processor job](/components/loglines-processor-job.md), along with the task configurations.
