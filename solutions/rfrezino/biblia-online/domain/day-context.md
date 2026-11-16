---
type: Domain Entity
title: DayContext
description: Entity holding AI-generated context for a day (and optional location), with processing status.
tags: [domain]
resource: backend/src/domain/entities/day_context.py
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

# Schema

- `DayContextKey(day: datetime, location: str | None)`: string form is `YYYY-MM-DD` or `YYYY-MM-DD_<location>`; used as the storage id.
- `DayContextStatus`: PENDING, PROCESSING, DONE, FAILED.
- `DayContext(key, date, context: str, status)`.

Persisted through the [DayContextRepository port](/domain/day-context-repository.md).
