---
type: Architecture
title: Stack and repository layout
description: Turborepo monorepo with a Next.js web app, Firebase Cloud Functions and an emulator-based local dev loop.
tags: [architecture, nextjs, firebase]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: pkg, resource: /package.json }
  - { id: web-claude, resource: /apps/web/CLAUDE.md, title: Web app agent guide }
---

# Layout

| Path | Role |
|------|------|
| `apps/web` | Next.js App Router app (MUI v9 + Tailwind) |
| `functions` | Firebase Cloud Functions, see [functions](/functions/index.md) |
| `packages` | Empty; reserved for shared code when the Expo app arrives |
| `specs/00N-*` | Spec-kit artifacts per feature, see [features](/features/index.md) |
| `firestore.rules`, `storage.rules` | Security rules, see [security rules](/data/security-rules.md) |
| `scripts/local` | Emulator seed data and guard scripts |

# Routing (apps/web/app)

* Public: `/[entity]/[lang]` (feed), `/daily-message/[date]`, `/members`, `/members/[memberSlug]`, `/news`. Unknown entity/language combinations 404.
* Admin: `/admin`, `/admin/[entity]/{settings,news,members,daily-messages}`, `/admin/themes`, `/admin/languages`.
* Auth/account: `/login`, `/register`, `/account/preferences`.

# UI conventions

* MUI is the component library; avoid raw HTML/Tailwind markup for UI. Brand logos use `react-icons`.[^web-claude]
* Entity theming sets CSS variables on a `data-entity` wrapper. `[data-entity] a` colour has higher specificity than MUI `sx`, so anchor-rendering MUI components need `!important`.[^web-claude]
* This Next.js version has breaking changes; read `node_modules/next/dist/docs/` before writing code.

# Local development

Firebase Emulator Suite runs in Docker (`npm run dev:local`), Next.js runs on the host. Project ID must be a `demo-*` ID so nothing can reach a real project; scripts refuse to start otherwise.[^pkg] `npm run test:local` runs unit, rules and function tests against a fresh emulator. Seeded admin accounts are documented in the README (emulator only).

[^pkg]: package.json
[^web-claude]: Web app agent guide
