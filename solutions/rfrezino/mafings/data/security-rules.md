---
type: Security Rules
title: Firestore security rules
description: Who can read and write each collection; admin by custom claim, comments only via callable functions.
tags: [security, firestore]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /firestore.rules
---

* Admin means `request.auth.token.admin == true`, see [constitution](/decisions/constitution.md).
* `entities` readable by anyone, write admin only. `members`, `dailyMessages`, `news` readable when `deletedAt == null`; admins also read soft-deleted docs so the admin UI can list them (fix in commit 310c3a2).
* `comments` and `replies`: client writes always denied; only [submitComment](/functions/submit-comment.md) and the delete callables write. Read allows `resource == null` because a `get` on a missing doc id otherwise throws a rules error (fix 7b9e1f0).
* Message and comment `likes/{uid}`: public read, only the owner writes.
* `users/{uid}`: owner read/update, but `isGlobalAdmin` cannot be changed by the client.
* `themes`, `languages`: admin-only read/write. `themes.deletedAt` cannot be set from the client (only [retireTheme](/functions/retire-theme.md)); hard delete is denied everywhere. Public pages resolve themes via the Admin SDK.
* Everything else: deny.
