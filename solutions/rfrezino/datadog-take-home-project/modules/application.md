---
type: "Module"
title: "Application CLI"
description: "Click CLI entry point for starting the monitor."
---
Command: `python src/application.py run start --w3c-logfile-path PATH`.

| Option | Default |
|---|---|
| `--run-w3c-logfile-reader-frequency_in_seconds` | 1 |
| `--enable-high-traffic-alert` | true |
| `--high-traffic-alert-loglines-per-second-threshold` | 10 |
| `--high-traffic-alert-window-in-minutes` | 2 |
| `--enable-report-statistics` | true |
| `--report-statistics-display-frequency-in-seconds` | 10 |
| `--log-level` | INFO |
| `--output-log-file-path` | empty (a logs/ file is created) |

Builds a [log monitor](/modules/log-monitor.md), then blocks: forever inside Docker, otherwise until the user types `exit`; then stops it.
