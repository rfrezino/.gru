---
type: "Decision"
title: "The prompt hooks live in the database and are edited by named admins, with the file as a seed"
description: "Hooks are stored as numbered versions, edited in the UI, over HTTP or with MCP tools by the people in GRU_HOOKS_ADMINS, checked like the file was, and applied live; GRU_HOOKS_FILE only seeds version 1."
---

# Context

[Decision 018](/decisions/018-prompt-hooks.md) made hooks operator policy loaded once from `GRU_HOOKS_FILE`. The owner then asked to adjust them in the UI ("we need to be able to adjust the hooks via ui"). A hook changes what every agent is told and can refuse every request, so an edit reaches every requester at once.

# Decision

- The database is the source of truth: `hooks_versions`, one row per save, the latest number is live, older rows are the history. The file seeds version 1 only when nothing is stored (same pattern as the GitHub allow-list file).
- Only the people in `GRU_HOOKS_ADMINS` read or change hooks (empty = everyone, development only). It is its own gate, not `GRU_GITHUB_ADMINS`: the two powers are different. The UI shows the link only when the read succeeds.
- A save is checked exactly like startup (limits, patterns, metered priced provider for an agent hook) and returns every problem by path; nothing is stored on a problem. Optimistic concurrency by version. An empty list is valid and turns hooks off.
- A save applies at once on the saving replica and within `GRU_HOOKS_REFRESH` on the others; a store that cannot be read keeps the last good set rather than dropping the policy.
- Never logged: hook text and patterns. The audit line has who, the version and the count.
- The same use cases serve the UI, HTTP (`/v1/hooks*`) and MCP (`get_hooks`, `validate_hooks`, `set_hooks`, `list_hook_versions`, offered only to those admins, never to a sandbox).

# Consequences

A request being rewritten by an agent hook when the set changes may fail closed; accepted and documented. Going back is saving an old version again (a new version). Per-workflow or per-requester hooks remain out of scope.
