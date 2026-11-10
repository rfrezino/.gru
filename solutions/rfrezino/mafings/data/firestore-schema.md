---
type: "Schema"
title: "Firestore schema"
description: "Collections and key fields, per specs/001-bible-entity-mvp/data-model.md."
---
# Schema

| Path | Notes |
|------|-------|
| `entities/{entityId}` | slug, name (map lang→string), languages, theme, features.newsEnabled, entityRoles, deletedAt |
| `.../members/{id}` | slug, type historical/fictional, localized name/description/teachings, imageUrls |
| `.../dailyMessages/{date}` | date as doc ID, localized text, likesCount, commentsCount |
| `.../dailyMessages/{id}/comments/{userId}` | one thread per user; repliesCount, likesCount; subcollections replies, likes |
| `.../dailyMessages/{id}/likes/{userId}` | idempotent like toggle |
| `.../news/{id}` | localized title/summary, sourceUrl, publishedAt |
| `users/{uid}` | displayName, email, photoURL, languagePref, favoriteEntities, isGlobalAdmin (mirror only) |

Localized fields are maps keyed by language code. All content has `deletedAt` ([principle](/architecture/constitution.md)). Counters are maintained by [functions](/architecture/cloud-functions.md). Later features add Languages registry and Themes collections.
