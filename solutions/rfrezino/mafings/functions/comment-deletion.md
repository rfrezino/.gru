---
type: Cloud Function
title: Comment deletion (deleteCommentThread, deleteCommentReply, onThreadSoftDeleted)
description: Soft-delete callables for threads and replies plus a trigger cascading a thread delete to its replies.
tags: [functions, comments, soft-delete]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /functions/src/deleteCommentThread.ts
---

* `deleteCommentThread` sets `deletedAt` on a thread; `onThreadSoftDeleted` cascades to every reply, so a thread and replies go as one unit.
* `deleteCommentReply` soft-deletes one reply (commit a353f01, which also fixed a bug where `commentsCount` was resurrected after deletion).
* Never hard-deletes, per [constitution](/decisions/constitution.md). Counters: [triggers](/functions/counter-triggers.md).
