---
type: "Decision"
title: "Code search is a separate mirror service reached only through gru-api"
description: "Sandbox agents search repositories without cloning them through a separate service that keeps shallow default-branch mirrors and runs git grep; the control plane mints its read tokens and is the only way in."
---

# Context

A sandbox agent sees only the files the control plane seeded (200 files, 16 MiB), so it cannot search a repository it was not given. The owner asked whether Sourcebot community edition could run beside gru, configured from gru's GitHub setup. A spike on Sourcebot v6.0.2 found: it needs its own Postgres and Redis; without a signed licence the deployment is "restricted" and every `/api/*` route and `/api/mcp` answer 403 `DEPLOYMENT_RESTRICTED` (indexing still works); MCP, OAuth, Ask and SSO are entitlements; the free "Basic plan" refreshes its licence weekly and phones home. GitHub's own `search/code` is rate-limited (about 10 calls a minute), default-branch only and has no regex, which is unreliable for an agent.

# Decision

- **A separate project, `codesearch/`** (module `gru/codesearch`, binary `gru-codesearch`, namespace `gru-search`) with its own API and its own MCP server. It keeps a shallow bare mirror of each allowed repository's default branch and searches it with `git grep` behind a `Searcher` port. No index, no licence, no product dependency beyond `git`.
- **The control plane mints the tokens, the service fetches.** gru-api exposes an internal listener (`:8082`, service tokens) that lists the catalog's repositories and mints and revokes one-repository `contents:read` installation tokens through the existing broker. The App key and the allow-list stay in gru-api; the service holds a token only for one fetch, in the git child's environment.
- **Sandboxes reach search only through gru-api.** Routes on the gateway listener, authenticated by the per-task `gru_gw_` token; gru-api resolves the requester, searches only repositories the requester may use (narrowed by the call; a run with one seeded repository defaults to it), applies each repository's allowed and protected paths as inclusions and exclusions, limits calls per task and forwards with its own token. The service is reachable only from gru-api (NetworkPolicy).
- **GitHub is touched only by the sync job, never on an agent tool call.** Mirrors are refetched every 15 minutes; every answer carries the commit and sync time.
- **Claude Code runner only** in the first version: four tools (`search_code`, `read_file`, `list_tree`, `list_repos`) in the existing `gru-agent mcp` bridge, switched on by `GRU_SEARCH=1` in the task env.

# Consequences

- No licence or phone-home, and agent load never reaches GitHub; the cost is mirror storage (about 48 MB for 26 repositories on the dev cluster; GKE needs a storage design, there is none yet) and `git grep` speed on very large repositories.
- Results are default-branch snapshots up to minutes old, not the commit a run was seeded from; documented in every tool's description.
- Three credentials, one per hop, none interchangeable; the token the bridge uses is the one the CLI already holds, passed as a literal in the MCP config (whether `${VAR}` expansion works in the pinned CLI was not checked).
- Call limits are per replica in memory (at most twice the setting with two replicas): they protect the service from a loop, they are not a quota.

# Alternatives and when to revisit

- **Sourcebot or Zoekt:** faster and symbol-aware; revisit if `git grep` is too slow or symbols are wanted; the `Searcher` port is the seam. Sourcebot's API and MCP are licence-gated, which is why it was dropped.
- **GitHub `search/code`:** no storage, but rate-limited and shallow; rejected for agents.
- **Other branches, in-process `gru-agent` providers, wiring the multi-repo `find` to this service:** deliberately out of the first version.
