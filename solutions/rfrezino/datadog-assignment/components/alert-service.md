---
type: Component
title: AlertService
description: Decides when the high traffic alert triggers and recovers.
resource: src/domain/monitor/alert_service.py
tags: [domain, alerting]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---
Fetches buckets covering the alert window from the [repository](/components/log-data-bucket-repository.md). If the covered timespan >= window and average req/s >= threshold, a [HighTrafficAlert](/components/alert.md) is created (unless one is already active). When the condition no longer holds and an alert is active, it is marked recovered. A recovered alert stays returned until a new one replaces it.

