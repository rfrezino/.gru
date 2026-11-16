---
type: Service
title: Logger and StructuredLogger
description: Logger port and its structlog implementation.
tags: [logging]
resource: backend/src/adapters/services/structured_logger.py
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

`Logger` (domain/services/logger.py) defines info, error, warning, debug, critical, exception, each taking a message and optional string fields. `StructuredLogger` implements it with structlog's KeyValueRenderer over stdlib logging. Registered in the DI container in [layers](/architecture/layers.md).
