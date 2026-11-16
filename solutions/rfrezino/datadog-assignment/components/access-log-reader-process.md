---
type: Component
title: AccessLogReaderProcess
description: Daemon process that parses log lines and groups them into one-second buckets.
resource: src/application/access_log_reader_process.py
tags: [application, multiprocessing]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
A multiprocessing Process (daemon). Reads lines from an [AccessLogStream](/components/access-log-stream.md), parses with [RawLogLineParser](/components/raw-log-line-parser.md), and fills a [LogDataBucket](/components/log-data-bucket.md) (timespan 1). When a line has a later timestamp than the current bucket, the bucket is added to the [repository](/components/log-data-bucket-repository.md). Known limitation: the last bucket is not flushed when traffic stops (IMPROVEMENTS.md).

