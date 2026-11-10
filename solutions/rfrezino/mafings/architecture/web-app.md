---
type: "Component"
title: "Web app (apps/web)"
description: "Next.js App Router frontend for public entity sites and the admin panel."
---
# Routes (apps/web/app)

- Public: `/[entity]/[lang]` feed, `daily-message/[date]`, `members`, `members/[memberSlug]`, `news`.
- Auth: `(auth)/login`, `(auth)/register`; `account/preferences`.
- Admin: `admin/` with `new`, `languages`, `themes`, and per-entity `[entity]/settings|members|daily-messages|news`.

# Libraries (apps/web/lib)

Per-domain repositories and types: `entities`, `members`, `dailyMessages`, `news`, `comments`, `likes`, `languages`, `themes`, `profile`; `auth` (AuthProvider, requireAdmin, useIsAdmin, syncProviderProfile); `firebase` (client/admin); `firestore/softDelete`; `theme` (color, hsl, contrast, presets, fonts); `translation` (swappable provider client + `useContentTranslation`).

Tests use Vitest (`vitest.config.ts`, plus `vitest.rules.config.ts` for rules-emulator tests).
