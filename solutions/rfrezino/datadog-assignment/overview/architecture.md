---
type: Architecture
title: Log monitor architecture
description: Hexagonal layout of the HTTP log monitoring CLI (domain, application, infrastructure).
tags: [architecture, python]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
# Overview
CLI tool that tails an access log (common log format), aggregates one-second buckets in a reader process, and renders a refreshing terminal dashboard with a high-traffic alert. Built from the Datadog take-home in CHALLENGE.md; see IMPROVEMENTS.md for known limitations.

# Layers
- Domain (`src/domain`): [LogData](/components/log-data.md), [LogDataBucket](/components/log-data-bucket.md), [LogDataBucketRepository](/components/log-data-bucket-repository.md), [RawLogLineParser](/components/raw-log-line-parser.md), [AlertService](/components/alert-service.md), [Alert](/components/alert.md), [LogMonitorStatus](/components/log-monitor-status.md), and the ports [AccessLogStream](/components/access-log-stream.md) and [LogMonitorPresenter](/components/log-monitor-presenter.md).
- Application (`src/application`): [AccessLogReaderProcess](/components/access-log-reader-process.md), [LogMonitorApplication](/components/log-monitor-application.md).
- Infrastructure (`src/infrastructure`): [FileAccessLogStream](/components/file-access-log-stream.md), [CliLogMonitorPresenter](/components/cli-log-monitor-presenter.md), signal handling.
- Entry point: [CLI](/components/cli-entrypoint.md).

# Data flow
tail -F -> FileAccessLogStream -> AccessLogReaderProcess (child process) parses lines and builds per-second buckets -> shared bucket repository (multiprocessing Manager list) -> LogMonitorApplication loop reads recent buckets every update_rate seconds and AlertService evaluates the alert window -> presenter prints.

See also [deployment](/overview/deployment.md).

