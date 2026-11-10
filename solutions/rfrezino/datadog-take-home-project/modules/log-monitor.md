---
type: "Module"
title: "Log monitor"
description: "Configures, starts and stops the reader and processor jobs."
---
`configure_log_monitor`, `initialize_log_monitor` and `stop_log_monitor` build an immutable `LogMonitor` holding the
[file reader job](/modules/file-reader-job.md) and the [processor job](/modules/loglines-processor-job.md), sharing a `ThreadSafeList[LogLine]`.
