---
type: Port
title: DayContextRepository
description: Abstract repository interface for DayContext.
tags: [domain, port]
resource: backend/src/domain/repositories/day_context_repository.py
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

# Interface

- `get(day_context_key) -> DayContext`
- `add(day_context) -> DayContextKey`

Implemented by [DynamoDbDayContextRepository](/adapters/dynamodb-day-context-repository.md). Note the implementation's `add` returns the DayContext, not the key, diverging from the declared signature.
