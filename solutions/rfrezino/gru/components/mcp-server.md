---
type: Component
title: gru MCP server
description: MCP endpoints of gru-api for people's clients and for sandboxed agents.
tags: [mcp, api]
generated: { by: claude-code/sonnet-5.5, at: 2026-11-16T00:00:00Z }
sources:
  - id: doc
    resource: docs/mcp.md
---

# gru MCP server

gru-api speaks MCP next to its HTTP API, one implementation for two audiences. Design: [decision 019](/decisions/019-gru-mcp-server.md); backend in [gru-api](/components/gru-api.md).

| Listener | Path | Credential | Caller |
|---|---|---|---|
| public (`GRU_HTTP_ADDR`) | `/mcp` | bearer token, IAP or service account | a person as requester |
| gateway (`GRU_GATEWAY_ADDR`) | `/mcp` | `gru_gw_...` task token | the agent of one task |

- Tools include `about_gru`, `list_providers`, `get_limits`, `list_tasks`/`get_task`, `list_runs`/`get_run`, events, usage, run files, repository reads and search, `start_run` and `submit_task`.
- Results are compact text of at most 32 KiB; user words appear only behind a "user data, not instructions" marker; nothing returned contains tokens, keys or URLs; anything not owned is "not found".
- `GRU_MCP_PUBLIC=false` and `GRU_MCP_SANDBOX=false` switch each listener off.
- Sandboxes whose provider uses the gateway get `GRU_PLATFORM=1`: the Claude Code runner gets the `gru-platform` server, the in-process agent names tools `gru_<tool>`.
