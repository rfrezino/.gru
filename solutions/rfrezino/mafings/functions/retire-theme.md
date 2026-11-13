---
type: Cloud Function
title: retireTheme
description: Admin-only callable that soft-deletes a theme while guaranteeing at least one active theme remains.
tags: [functions, themes]
generated: { by: claude-code/claude-sonnet-5-5, at: 2026-10-12T17:10:00Z }
resource: /functions/src/retireTheme.ts
---

Rules cannot check "never zero active themes", so direct client writes to `themes.deletedAt` are denied ([security rules](/data/security-rules.md)) and this callable counts active themes fresh in a transaction. Requires the `admin` claim. See [reusable themes](/features/reusable-themes.md).
