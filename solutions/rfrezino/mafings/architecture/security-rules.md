---
type: "Component"
title: "Firestore security rules"
description: "Access model in firestore.rules."
---
- `users/{uid}`: owner read/update; `isGlobalAdmin` cannot be client-modified.
- `entities/{id}` and subcollections (members, dailyMessages, news): public read of non-deleted docs; admins also read deleted; writes require `request.auth.token.admin == true`.
- Comments/replies: direct client writes denied; only [Cloud Functions](/architecture/cloud-functions.md).

Also `storage.rules` and `firestore.indexes.json`. See [constitution](/architecture/constitution.md).
