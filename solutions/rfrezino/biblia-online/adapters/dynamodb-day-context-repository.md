---
type: Adapter
title: DynamoDbDayContextRepository
description: DynamoDB implementation of the DayContext repository using boto3.
tags: [adapter, dynamodb]
resource: backend/src/adapters/repositories/dynamodb_day_context_repository.py
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

# Behaviour

- Constructed with `endpoint_url` and optional region; creates the `DayContext` table in the constructor (hash key `id` string, 5/5 provisioned capacity). It does not handle an already-existing table.
- Items: `id`, `date`, `context`, `status`, `ob_updated_at` (all strings).
- `get` raises a generic `Exception("Item not found")` when missing.
- boto3 client lazily created with 5s timeouts and 2 retry attempts.

Implements [DayContextRepository](/domain/day-context-repository.md) for the [DayContext](/domain/day-context.md) entity. Integration test: `backend/tests/integration/adapters/repositories/test_dynamodb_day_context_repository.py`. Local DB: [local development](/operations/local-development.md).
