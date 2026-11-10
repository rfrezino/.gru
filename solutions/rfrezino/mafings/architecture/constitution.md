---
type: "Decision Record"
title: "Project constitution (core principles)"
description: "Five governing principles from .specify/memory/constitution.md."
---
# Principles

1. **Entity isolation** — entities never cross-reference; data lives under `entities/{entityId}` subcollections.
2. **Soft delete only** — nullable `deletedAt`; every read path filters it.
3. **Server-enforced sensitive writes** — comments etc. go through callable Functions.
4. **Security boundary via custom claims** — the `admin` Auth claim, not `isGlobalAdmin`, is authoritative.
5. **Simplicity & MVP discipline.**

Constraints: App Check enabled; unsupported entity/language routes 404; schema must not hard-code a single language; no ads (affiliate links and per-entity tokens). See [security rules](/architecture/security-rules.md).
