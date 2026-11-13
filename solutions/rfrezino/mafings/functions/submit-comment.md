---
type: Cloud Function
title: submitComment
description: Callable that creates or edits a comment thread or reply under a 20s cooldown and 500 char cap, inside one transaction.
tags: [functions, comments, rate-limit]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /functions/src/submitComment.ts
---

# Contract

Request: `{ entityId, messageId, text, replyToThreadUid? }`. Requires auth. Text is trimmed, non-empty, at most 500 characters.

# Behaviour

* Without `replyToThreadUid` it creates or edits the caller's own thread (doc id = uid). Editing is refused once `repliesCount > 0`.
* The 20s cooldown reads `users/{uid}.rateLimits.lastCommentAt` fresh inside the transaction and is shared by threads and replies.
* All reads precede writes in the transaction, so concurrent requests cannot bypass cooldown or edit-lock. This is [constitution](/decisions/constitution.md) principle 3.

Schema: [Firestore model](/data/firestore-model.md).
