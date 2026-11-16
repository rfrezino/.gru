---
type: Use Case
title: DayContext use cases
description: GenerateDayContextUseCase and GetDayContextUseCase (stubs).
tags: [usecase]
status: draft
resource: backend/src/adapters/usecases/
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

- `GenerateDayContextUseCase.execute(key)`: only reads the repository for the key and returns it; no AI generation yet.
- `GetDayContextUseCase`: empty class.

Both depend on the [repository port](/domain/day-context-repository.md); neither is exposed through the API.
