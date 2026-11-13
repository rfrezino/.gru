---
type: Service
title: Code-search service (codesearch/)
description: A Go service that keeps shallow default-branch mirrors of the repositories the catalog allows and answers grep, read and tree calls from gru-api, with its own API and MCP server.
resource: codesearch/
tags: [service, codesearch, github, mirrors]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-22T13:00:00Z }
sources:
  - id: decision
    resource: /decisions/016-code-search-is-a-separate-mirror-service.md
    title: Code search is a separate mirror service
  - resource: codesearch/README.md
  - resource: docs/codesearch.md
  - resource: dev/codesearch/codesearch.yaml
  - resource: dev/scripts/codesearch-up.sh
---

> Superseded in part by [decision 021](/decisions/021-python-control-plane.md): the service is Python (`codesearch/gru_codesearch`); routes, limits and behaviour are unchanged.

# Contents

- `gru-codesearch` (`codesearch/`, onion layout with a layering test): a sync job (list from gru-api, a minted read token per fetch, shallow bare clone or fetch, state files), a search service over `git grep` (fixed argv, scoped by the include and exclude paths it is given), an HTTP API (`/v1/search|read|tree|repos`) and an MCP server (`/mcp`) behind bearer tokens, and metrics on their own port.
- Runs in namespace `gru-search` on a 20Gi volume, hardened (non-root, read-only root, no service-account token), default-deny network policy; only gru-api reaches it and it reaches only gru-api's internal listener and GitHub's HTTPS.
- gru-api side: the internal listener (`internalapi`), the sandbox-facing routes on the gateway listener (`codesearchgw`, `app.CodeSearchService`, `app.CallLimiter`, the `codesearch` client) and `GRU_SEARCH=1` in a metered claude-code task's env; agent side: the four tools in `internal/mcpbridge`. See [gru-api](/components/gru-api.md) and [task image](/components/task-image.md).
- Commands: `make codesearch-image|up|down|unit|integration|acceptance|cluster-test|real`. Full guide: `docs/codesearch.md`.
