---
type: "Module"
title: "Log lines processor job"
description: "Thread that periodically runs report and alert tasks."
---
Loop: consume new lines from the shared list, append to accumulated lines, run the [report task](/modules/report-task.md) and
[alert task](/modules/alert-task.md), sleep. State is kept in `MonitorStatus`. Any exception terminates the thread silently.
