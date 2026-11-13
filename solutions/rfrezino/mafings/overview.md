---
type: Project
title: Meusite / wisdomfeed overview
description: Multi-entity platform for belief systems and philosophies, each a self-contained mini-site with daily messages, members, news and comments.
tags: [overview, product]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: readme, resource: /README.md, title: Project vision README }
  - { id: constitution, resource: /.specify/memory/constitution.md, title: Project constitution }
---

# What it is

A platform where each **entity** (a religion, philosophy or topic) is a separate mini-site at `/<entity>/<lang>`, e.g. `/bible/pt`.[^readme] Entities share features and UX; only branding and content differ. Entities must never cross-navigate or reference each other.[^constitution]

# Current state

Only the `bible` entity exists, Portuguese (`pt`) first. Shipped: daily message feed with likes/comments/replies, members, news, admin panel, reusable themes and languages, AI-assisted translation (provider stub only), profile editing.

# Planned, not built

Chat with AI-impersonated members, per-entity tokens, affiliate links, Expo mobile app, per-entity admin delegation. See [open risks](/decisions/open-risks.md).

# Map

* [Architecture](/architecture/stack.md)
* [Firestore data model](/data/firestore-model.md)
* [Design principles](/decisions/constitution.md)
* [Cloud Functions](/functions/index.md)
* [Features](/features/index.md)

[^readme]: Project vision README
[^constitution]: Project constitution
