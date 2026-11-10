---
type: "Service"
title: "Backend overview"
description: "FastAPI backend with hexagonal layering for the Biblia Online AI app."
---
Python (3.13, uv) FastAPI service in `backend/`, entry point `src/main.py`. Uses lagom for DI (a `Logger` bound to `StructuredLogger`), permissive CORS, and a lifespan hook that only logs.

Layers under `src/`:
- `domain/`: entities, repository ABCs, `Logger` ABC. See [day context](/backend/day-context.md).
- `adapters/`: [DynamoDB repository](/backend/dynamodb-repository.md), structlog logger, use cases (`GenerateDayContextUseCase` only calls `get`; `GetDayContextUseCase` is an empty stub).
- `host/restapi/`: see [REST API](/backend/rest-api.md).

The project is an early skeleton; the root README is just "Biblia Online com IA". See [local development](/backend/local-development.md).
