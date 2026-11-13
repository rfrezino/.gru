---
type: Feature
title: Daily message feed
description: Vertical snap-scrolling infinite carousel of daily messages with deep links, love, comments and sharing.
tags: [feature, feed, ux]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /apps/web/components/dailyMessage/Feed.tsx
sources:
  - { id: web-claude, resource: /apps/web/CLAUDE.md }
---

# Design intent (do not regress)[^web-claude]

* One `MessageCard` centered at a time (`scroll-snap-align: center`), neighbours peek above and below. All cards share one size; a compact variant for peeking cards was tried and reverted.
* Comments for the centered card render below the carousel via `CommentList`, so thread height does not resize slides.
* The carousel fills the remaining viewport height, not a fixed px/dvh height.
* Card width capped at `44rem` and centered; edge-to-edge was rejected as too wide.
* Permalink `/[entity]/[lang]/daily-message/[date]` supports deep links (commit b334cdd).

# Flexbox gotcha

"Fill remaining height" needs `min-h-0` on **every** flex ancestor from `<body>` down, and `<body>` must use `h-full`, not `min-h-full`. Missing one link once inflated a ~1200px container to 12,614px.

# Behaviour

Likes are idempotent per user, comments go through [submitComment](/functions/submit-comment.md): one thread per user, edit-lock after first reply, 20s cooldown. Data: [Firestore model](/data/firestore-model.md).

[^web-claude]: Web app agent guide
