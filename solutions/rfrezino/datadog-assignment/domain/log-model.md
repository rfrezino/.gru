---
type: "Domain Model"
title: "Log data model and parsing"
description: "LogData, LogDataBucket, repository, stream port and line parser"
---

- `LogData`: bytes_sent, final_status, remote_host, remote_user, timestamp, section.
- `LogDataBucket`: timestamp, timespan, requests, total bytes and counters per status/host/user/section; `add_log_data` and `+=` merge buckets.
- `LogDataBucketRepository(keep_buckets)`: multiprocessing-Manager list shared between processes; prunes old buckets; `get_by_timespan` and `get_since_timestamp` merge newest buckets.
- `RawLogLineParser`: uses apachelogs COMMON; section is the first path segment (or `/`).
- `AccessLogStream`: abstract context-manager port yielding lines.

Used by [reader](/application/access-log-reader-process.md) and [alerting](/domain/alerting.md).
