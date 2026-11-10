---
type: "Domain Entity"
title: "DayContext"
description: "Per-day (optionally per-location) AI context record and its repository contract."
---
Defined in `src/domain/entities/day_context.py`.

- `DayContextKey(day, location|None)`: string form is `YYYY-MM-DD` or `YYYY-MM-DD_<location>`.
- `DayContextStatus`: PENDING, PROCESSING, DONE, FAILED.
- `DayContext(key, date, context, status)`.
- `DayContextRepository` (ABC): `get(key)`, `add(day_context)`. Implemented by [DynamoDB repository](/backend/dynamodb-repository.md).
