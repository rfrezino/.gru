---
type: Schema
title: Firestore data model
description: Collections, key fields and denormalized counters for entities, content, comments, themes and languages.
tags: [firestore, schema]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
sources:
  - { id: dm, resource: /specs/001-bible-entity-mvp/data-model.md }
  - { id: dm4, resource: /specs/004-reusable-themes/data-model.md }
---

# Schema

| Path | Notes |
|------|-------|
| `entities/{id}` | `slug` (immutable), `name` map by lang, `languages[]`, `themeId`, `logoUrl`, `features.newsEnabled`, `entityRoles` (reserved, empty), `deletedAt`.[^dm] |
| `entities/{id}/members/{id}` | `slug`, `type` historical/fictional (no behaviour yet), per-language maps: name, description, historicalContext, teachings, appearance, extras; `imageUrls`. |
| `entities/{id}/dailyMessages/{date}` | Doc ID is `YYYY-MM-DD`, so one per entity per day. `text` map by lang, `sourceMemberId`, `likesCount`, `commentsCount`. |
| `.../dailyMessages/{id}/likes/{uid}` | Doc ID = uid makes the toggle idempotent. |
| `.../dailyMessages/{id}/comments/{uid}` | One thread per user per message. `repliesCount`, `likesCount`; editable only while `repliesCount == 0`. Soft delete cascades to replies. |
| `.../comments/{uid}/replies/{id}` | Many per thread; text immutable. |
| `.../comments/{uid}/likes/{likerId}` | Likes on a thread; drives most-liked ordering. |
| `entities/{id}/news/{id}` | Per-language title/summary. |
| `themes/{id}` | Top-level reusable `primaryColor`, `secondaryColor`, `fontFamily`; entity stores only `themeId`.[^dm4] |
| `languages/{code}` | Registered language catalog. |
| `users/{uid}` | Profile, `isGlobalAdmin` (display only), `rateLimits.lastCommentAt`. |

# Conventions

* Localised text is `map<lang, string>`, with `primaryLanguage` and `translationStatus` on content items, see [AI translation](/features/content-translation.md).
* Themes are resolved at read time in `lib/entities/repository.ts`, no copy onto entities. See [reusable themes](/features/reusable-themes.md).
* Counters are maintained by [triggers](/functions/counter-triggers.md), never by clients.

[^dm]: Bible entity MVP data model
[^dm4]: Reusable themes data model
