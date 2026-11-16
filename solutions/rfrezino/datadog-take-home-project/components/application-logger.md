---
type: Source Module
title: application_logger
tags: [log-monitor]
description: Logger setup with console and flushed file handler
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/application_logger.py

`get_application_logger(log_level, output_log_file)` configures root logging with a stream handler plus a file handler that flushes on every record. An empty path creates `logs/%Y_%m_%d_%H_%M_%S-log-monitor-app.log` in the cwd. Used by all jobs and tasks to emit reports and alerts. Part of [overview](/overview.md).
