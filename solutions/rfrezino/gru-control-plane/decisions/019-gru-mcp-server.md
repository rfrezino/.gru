---
type: "Decision"
title: "gru is an MCP server, for people and for its own agents"
description: "gru-api serves MCP over streamable HTTP on the public listener (people's own clients) and on the gateway listener (the agent of a task, by its token); one adapter over the existing use cases, text results, read-only for sandboxes except delegation behind limits, and about_gru as the one text that says what gru is."
---

> Superseded in part by [decision 021](021-python-control-plane.md): the server is Python on the `mcp` 2.x SDK (`host/mcp`), and the Go SDK references no longer apply; tool names, audiences, limits and delegation are unchanged.

# Context

An agent asked "which repositories do I have?" could not know. The first answer was a long hook text; the owner then asked for gru itself to be an MCP server available to the runners, and to people, so that runs can be started and followed and repositories and workflows read from any MCP client. The search tools lived in the stdio bridge of the sandbox agent, with a JSON route and a handler per tool.

# Decision

- **One server in gru-api, over HTTP,** mounted on the public listener (`/mcp`, the API's own authenticators) and on the gateway listener (`/mcp`, `Authorization: Bearer gru_gw_...` through `AdmitTask`). The Claude Code runner gets it as the HTTP server `gru-platform` in its `--mcp-config` (allowed as a whole: the server decides which tools a task sees), the in-process runner through an MCP client behind its tool box (`gru_<tool>`). `GRU_PLATFORM=1` in the task env says it is on.
- **The listener resolves the caller;** a tool never sees a credential. The tool set derives from the caller: people, sandbox, delegating sandbox, admin (only a person the GitHub admin gate admits).
- **Tools are thin calls to use cases;** the shared read models (`TaskDetail`, `RunDetail`, `PlatformFacts`) live in `app` and the HTTP handlers use them too.
- **Text results, bounded to 32 KiB, user text labelled** and returned only by the tools made to show it. Errors are fixed sentences; arguments and results are never logged; labels in metrics are fixed words.
- **Sandboxes read, and may start work only with `GRU_MCP_DELEGATION`:** the child is the requester's, one-shot, metered, depth- and count-limited, paid out of the parent's own budget as a held ledger reservation settled at the child's spend, and cancelled when the parent ends. An agent never saves workflows, answers questions or uses admin tools.
- **The search tools moved from the stdio bridge to this server;** `gru-agent mcp` serves `ask_user` only. The gateway's `/codesearch/v1/*` routes stay.
- **`about_gru` is the one text that says what gru is** (an embedded file, checked against the tool list by a test); the dev hook keeps only the per-person facts and points at it.
- Defaults: both listeners on, delegation off.

# Consequences

- A tool is written once and serves people, Claude Code stages and in-process stages.
- The SDK's Host-header check is off (service-name callers); every call is bearer-authenticated.
- Any new route that returns user text must say how it is labelled; the sandbox tool set is a policy surface that tests pin.
- Children are counted by the row, not atomically with the hold: two simultaneous starts from one task can exceed the child cap by one.
- A child run cancelled by its parent's end keeps its spend on its own ledger only.
- The `gru` CLI can be retired once the parity matrix in its plan has no gap left.

# Alternatives and when to revisit

- **More tools in the stdio bridge:** three places per tool and nothing for people; dropped.
- **A separate MCP service:** nothing the tools need lives outside gru-api; revisit only for isolation.
- **MCP resources and prompts:** tools only for now; revisit when a client benefits.
