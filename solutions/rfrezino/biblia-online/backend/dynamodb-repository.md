---
type: "Repository Adapter"
title: "DynamoDbDayContextRepository"
description: "DynamoDB implementation of DayContextRepository."
---
`src/adapters/repositories/dynamodb_day_context_repository.py`; implements the contract in [day context](/backend/day-context.md).

- Table `DayContext`, hash key `id` (string, the key's string form), capacity 5/5.
- Attributes: id, date, context, status, ob_updated_at.
- Constructor takes `endpoint_url` and optional region, and calls create_table immediately (fails if the table already exists).
- `get` raises a generic Exception when the item is missing.
- boto3 client: 5s connect/read timeouts, 2 attempts.
- Integration test: `tests/integration/adapters/repositories/test_dynamodb_day_context_repository.py`.
