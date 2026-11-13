---
type: Decision
title: Project constitution (five principles)
description: Non-negotiable design rules governing data layout, deletion, sensitive writes, authorization and scope.
tags: [principles, security]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: constitution, resource: /.specify/memory/constitution.md }
---

1. **Entity isolation.** Data lives in subcollections under `entities/{entityId}`, never top-level with an `entityId` field.[^constitution]
2. **Soft delete only.** Content gets a nullable `deletedAt`; every read path, including direct URLs, filters it out. User accounts are exempt pending legal review.
3. **Server-enforced sensitive writes.** Rate-limited or capped writes go through callable Cloud Functions with a transaction that reads state fresh. Rules and client checks are only defence in depth. See [submitComment](/functions/submit-comment.md).
4. **Security boundary via custom claims.** Admin is the Auth claim `admin: true`; `users/{uid}.isGlobalAdmin` is display only. See [security rules](/data/security-rules.md).
5. **Simplicity and MVP discipline.** Reserve schema for future phases (e.g. `entityRoles`) but add no behaviour until planned.

Also required: App Check on Firestore/Functions/Storage, `pt` first without single-language assumptions, no ads, a procurement ticket for any paid service.

[^constitution]: Project constitution
