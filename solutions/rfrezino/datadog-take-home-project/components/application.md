---
type: Source Module
title: application.py CLI
tags: [log-monitor]
description: click CLI entry point (run start)
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
sources:
  - id: repo
    resource: https://github.com/rfrezino/datadog-take-home-project/tree/57801b5
    title: rfrezino/datadog-take-home-project at 57801b5
---
# src/application.py

click group `cli` > `run` > `start` command. Builds a logger ([application_logger.py](/components/application-logger.md)), configures and starts the [log monitor](/components/log-monitor.md), then blocks: forever in Docker (detected by `docker_utils.is_docker_environment`, which inspects `/proc/self/cgroup`), otherwise until the user types `exit`. Then stops the monitor.

# Options
| Option | Default | Meaning |
|---|---|---|
| --w3c-logfile-path | required, must exist | file to watch |
| --run-w3c-logfile-reader-frequency_in_seconds | 1 | read frequency (also used for processor frequency) |
| --enable-high-traffic-alert | True | enable alert |
| --high-traffic-alert-loglines-per-second-threshold | 10 | hits/second threshold |
| --high-traffic-alert-window-in-minutes | 2 | analysis window |
| --enable-report-statistics | True | enable report |
| --report-statistics-display-frequency-in-seconds | 10 | report interval |
| --log-level | INFO | logging level |
| --output-log-file-path | '' | log file; default `logs/<datetime>-log-monitor-app.log` under cwd |

Note: both flags are `is_flag` with default True, so they cannot be switched off from the CLI.
