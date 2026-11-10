---
type: "Project"
title: "Wisdomfeed overview"
description: "Multi-entity platform for belief systems with daily content, members, and AI chat."
---
# Overview

A platform of "entities" (religions, philosophies, topics), each a self-contained mini-site at `<domain>/<entity>/<lang>` (e.g. `/bible/pt`). Entities share features (daily messages, news, members, comments/likes, themes) but differ in branding and content. Planned later: AI chat with members, tokens/payments, multi-admin delegation, mobile app.

Stack: npm workspaces + Turborepo monorepo (`apps/web` Next.js App Router + Tailwind, `functions` Firebase Cloud Functions, `packages` reserved for a future Expo app), Firebase Auth/Firestore/Storage/App Hosting.

See [architecture](/architecture/index.md), [data model](/data/firestore-schema.md), [principles](/architecture/constitution.md).
