---
type: Cloud Function
title: Counter triggers (onLikeWrite, onCommentLikeWrite, onCommentThreadWrite, onUserCreate)
description: Firestore triggers that maintain likesCount, commentsCount and repliesCount and create user docs.
tags: [functions, triggers, counters]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /functions/src/onLikeWrite.ts
---

* `onLikeWrite` adds +1/-1 to a message's `likesCount` when a like doc is created or removed. `onCommentLikeWrite` does the same for thread likes.
* `onCommentThreadWrite` maintains `commentsCount` and `repliesCount`.
* `onUserCreate` initialises `users/{uid}`.
* Counters are server-owned so the feed renders fast without reading subcollections and clients cannot spoof counts. See [data model](/data/firestore-model.md).
