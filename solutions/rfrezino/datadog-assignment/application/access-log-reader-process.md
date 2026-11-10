---
type: "Application Service"
title: "AccessLogReaderProcess"
description: "Daemon process turning log lines into per-second buckets"
---

A `multiprocessing.Process` that reads lines from an [AccessLogStream](/domain/log-model.md), parses them and groups them into 1-second `LogDataBucket`s stored in the shared repository.
