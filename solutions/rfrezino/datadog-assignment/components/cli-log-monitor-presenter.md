---
type: Component
title: CliLogMonitorPresenter
description: Full-screen terminal dashboard.
resource: src/infrastructure/monitor/cli_log_monitor_presenter.py
tags: [infrastructure, ui]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Uses blessings for fullscreen, pyfiglet for the header, tabulate for tables and hurry.filesize for sizes. Shows 'Waiting for data...' when no data, otherwise request rate, response size and top sections/status/hosts/users. Alerts print in red when active and a recovery message when recovered. Implements [LogMonitorPresenter](/components/log-monitor-presenter.md).

