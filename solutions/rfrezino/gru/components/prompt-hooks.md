---
type: Component
title: Prompt hooks
description: Operator-defined steps between a request and the agent, from GRU_HOOKS_FILE.
tags: [hooks, policy]
generated: { by: claude-code/sonnet-5.5, at: 2026-11-16T00:00:00Z }
sources:
  - id: doc
    resource: docs/hooks.md
---

# Prompt hooks

An operator file (`GRU_HOOKS_FILE`, version 1, at most 3 hooks) applies to every task and run; users cannot turn it off. A bad file stops the server at start. Design: [decision 018](/decisions/018-prompt-hooks.md).

- **instructions**: no model call; text goes into the agent's system message (prepend or append); `block_if` RE2 patterns refuse a request with a 400 naming the hook only. A composed message over 16 KiB fails closed.
- **agent**: a model rewrites the request through the gateway with its own required `budget_usd` and a timeout; it has no tools and is not listed with the requester's tasks.
- Per-request facts in double braces: `{{requester}}`, `{{stage}}`, `{{repo}}`, `{{repos}}`, `{{providers}}`, `{{limits}}`, `{{workflows}}`; unknown names are refused.
- Worked example: `dev/hooks/dev.yaml`. Hooks point agents at [the MCP server](/components/mcp-server.md) for what gru is.
