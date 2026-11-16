---
type: Project
title: biblia-online overview
description: Online Bible with AI; currently a Python FastAPI backend skeleton.
tags: [overview]
generated:
  by: claude-code/sonnet-5.5
  at: '2026-11-16T22:15:00Z'
---

# Overview

"Biblia Online com IA" — an online Bible with AI. The repo contains only a `backend/` service so far (no frontend). Architecture: [hexagonal layers](/architecture/layers.md). Domain: [DayContext](/domain/day-context.md). Ops: [local development](/operations/local-development.md).

Status: early scaffold. The only live endpoint is [/health](/api/health-endpoint.md); use cases are stubs.
