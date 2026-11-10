---
type: "Component"
title: "Cloud Functions"
description: "Callable and trigger functions exported from functions/src/index.ts."
---
| Function | Role |
|----------|------|
| onUserCreate | user bootstrap on Auth creation |
| submitComment | create/edit comment threads and replies; 20s cooldown, edit-lock |
| deleteCommentThread / deleteCommentReply | moderation deletes (soft) |
| onThreadSoftDeleted | cascades soft delete to replies |
| onLikeWrite / onCommentLikeWrite / onCommentThreadWrite | maintain denormalized counters |
| retireTheme | retire a reusable theme |
| translateContentFields | AI translation behind a swappable provider ([feature 005](/features/005-content-translation.md)) |

Rationale: [security rules](/architecture/security-rules.md) deny direct client writes to comments.
