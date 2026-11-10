---
type: "Project Overview"
title: "HTTP log monitor overview"
description: "Python console app that tails a W3C access log, prints periodic statistics and high-traffic alerts."
---
# Overview

Take-home project: a console program that consumes an actively written W3C/common-format HTTP access log
(`remotehost rfc931 authuser [date] "request" status bytes`).

Pipeline: [file reader job](/modules/file-reader-job.md) reads new lines, [parser](/modules/w3c-parser.md)
and a mapper convert them to `LogLine` objects in a thread-safe list, and the
[processor job](/modules/loglines-processor-job.md) consumes them to run the [report](/modules/report-task.md)
and [alert](/modules/alert-task.md) tasks. Wiring lives in [log-monitor](/modules/log-monitor.md); the CLI is [application](/modules/application.md).

Layout: `src/` (w3c_log, file_watcher, mapper, loglines, tasks, jobs, log_monitor, thread_utils, docker_utils),
`tests/` (unit_tests, integration_test, acceptance_test). Tests run via `make run_unit_tests`,
`make run_integration_tests`, `make run_acceptance_tests`; Dockerfile and Pipfile provided.
