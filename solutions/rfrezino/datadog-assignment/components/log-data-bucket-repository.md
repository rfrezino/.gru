---
type: Component
title: LogDataBucketRepository
description: Process-shared rolling store of buckets.
resource: src/domain/log/log_data_bucket_repository.py
tags: [domain, multiprocessing]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Stores [buckets](/components/log-data-bucket.md) in a multiprocessing Manager list, pruned to keep_buckets. get_by_timespan(n) merges latest buckets until n seconds are covered; get_since_timestamp(t) merges buckets newer than t. Both return deep copies/merges, or None when empty.

