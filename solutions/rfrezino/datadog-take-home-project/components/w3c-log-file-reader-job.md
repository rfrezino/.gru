---
type: Source Module
title: W3C log file reader job
tags: [log-monitor]
description: Producer thread that tails the log file
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/jobs/w3c_log_file_reader_job.py

`_W3cLogFileReaderJob` (Thread) loops until terminated: reads new lines from the last seek position ([file reader](/components/file-reader.md)), parses ([parser](/components/w3c-parser.md)), maps ([mapper](/components/logline-mapper.md)), extends the shared list, sleeps. Lines that fail parsing are dropped silently. Wrapped by immutable `W3cLogFileReaderJob` with `configure_/initialize_/stop_w3c_logfile_reader_job`.
