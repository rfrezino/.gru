---
type: Architecture
title: Backend layers
description: Hexagonal/ports-and-adapters layout of backend/src.
tags: [architecture]
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

# Layout (backend/src)

| Package | Role |
|---|---|
| `domain/` | entities, abstract repositories and services (ports) |
| `adapters/` | implementations: DynamoDB repository, structlog logger, use cases |
| `host/restapi/` | FastAPI routers and routing helpers |
| `main.py` | app creation, CORS (all origins), lagom DI container, lifespan logging |

Dependencies are wired in `setup_container` in main.py using [lagom](https://github.com/meadsteve/lagom); only `Logger` -> [StructuredLogger](/services/logger.md) is registered. The DayContext repository is not yet registered.

Concepts: [DayContext](/domain/day-context.md), [repository](/adapters/dynamodb-day-context-repository.md), [use cases](/adapters/day-context-use-cases.md), [health endpoint](/api/health-endpoint.md).
