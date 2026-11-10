---
type: "Decision"
title: "The control plane is Python, in a new repository, with servify's layers and a frozen compatibility surface"
description: "gru moved from five Go modules to Python (uv workspace, async ports, domain/adapters/host layers enforced by import-linter, Lagom in the composition root, Temporal over a pure interpreter, mcp 2.x); the task runner stays Go and the sandbox agent is Python again; routes, tools, env vars, metrics and the schema did not change."
---

# Context

The owner decided to rebuild gru in Python, using the sibling Python service `servify` (hexagonal: `domain` / `adapters` / `host`) as the structural reference. Go gru (five modules, 498 `.go` files plus the UI) was in good shape; the move is a choice about the language the team maintains, not a repair. Work in progress in Go (multi-tenancy phases 2 to 5, prompt-hook changes, runs on a repository, the feature skills) was held until the move was done. Three things had to be true for a port to be safe: behaviour had to stay observable-identical, the layering had to be enforced rather than remembered (servify itself had one `domain` to `adapters` import and no check), and nothing from the Go repo's secrets could travel.

# Decision

- **A new repository, `gru-control-plane`;** the Go repo stayed the behavioural reference at its tag `pre-python-migration` (commit `e2f993e`) and is archived (bundle, tarball, sha256) and then deleted by the owner, by hand, after the parity gate (`docs/migration/deletion-checklist.md`). A script never deletes it.
- **Python first on 3.14, then a gated look at 3.15.** The 3.14 pin was chosen because Temporal, MCP, protobuf and the LLM SDKs listed it and 3.15 was not yet listed; a gate target runs the whole suite on 3.15 before any pin moves, and its result is in `docs/migration/py315-result.md`. The current pin is stated in one line of `CLAUDE.md`. The 3.15-readiness rules apply from the first line (explicit `encoding="utf-8"`, no `isinstance(x, dict)` where a `frozendict` could appear, no `__cached__`).
- **One uv workspace, servify's layers inside each service.** `api`, `codesearch`, `providers` and `images/task/agent` each have `domain/` (entities, `I*` ports, use cases: standard library only), `adapters/`, `host/`, `platform/` and `container.py`. Use cases live in `domain`, not in an adapters folder. Layering is **tested**: import-linter contracts (`make layering`) plus a test that proves a violation trips them; each SDK (`sqlalchemy`, `temporalio`, `mcp`, ...) is confined to its seam.
- **Async ports, Lagom only in the composition root.** Every I/O port method is `async def`; background loops are asyncio tasks; optional features are `None` constructor arguments. No domain or host code imports Lagom. Money stays integer micro-USD (`Micros`), never `float`; time and ids are injected.
- **Contract suites subclassed per adapter.** Every port has one suite that the in-memory and the real adapter each pass (the Go `repotest`/`runtimetest` pattern), written before the code and run first against memory.
- **Engine: Temporal over a pure interpreter.** `interpret_run` has no clock, I/O or `temporalio` import; the Temporal workflow wraps it, `workflow.patched` replaces `GetVersion`, and recorded histories replay in tests.
- **Code-first spec.** (Superseded the spec-first-by-test rule on 2026-11-03.) No OpenAPI file is committed: each route carries `spec(...)`, the document is generated from the app and feeds the UI's generated types; the contract test holds it to the app (every operation routed, protected, annotated, answering its schema) and golden bodies pin the bytes.
- **MCP with the official `mcp` 2.x SDK,** same tool names, audiences and limits.
- **The runner stays Go, the sandbox agent is Python again.** `images/task/runner` moves unchanged and is built inside the pinned AX checkout; it is the only Go in the repo. The sandbox agent was Go by [decision 010](010-pluggable-llm-providers-through-a-catalog.md) ("everything is Go", Python removed from the image); it is now Python behind the same `Provider` port, with the `anthropic` and `openai` SDKs (`any-llm` did not cover the vendors needed). Python is therefore back in the task image on purpose.
- **The compatibility surface is frozen** (migration Decision 3): HTTP routes and shapes, MCP tool names and schemas, `GRU_*` variables and defaults, metric names and fixed label values, ports, header names and bearer prefixes (`gru_k_`, `gru_gw_`, `ate-target-actor`), the `/workspace` file contract, the end-state schema (one Alembic baseline equal to migration 0021) and the `make` target names. `deploy/`, `dev/` and `ui/` carried over with path edits. **One deliberate change:** the seed organization `0` is `platform` (the Go seed carried a company name), and examples use `acme/...`.
- **Tests keep their tiers and folders** (`<project>/tests/{unit,integration,acceptance}`, `tests/e2e`); Go build tags became pytest markers; a missing dependency fails, never skips.

# Consequences

- The Go suites were the oracle: each ported unit cites its Go file, and the parity report maps every Go test file to a Python counterpart or a written reason (`docs/migration/parity-report.md`, checked by a test).
- Differences that remain are recorded, not hidden: hook patterns use Python `re` (RE2 syntax such as a mid-pattern `(?i)` is refused at save); glob matching in code search is a linear matcher, not a regex; the process metrics differ from Go's runtime collector; a non-operator sending a bad `page_size` to an admin route gets 404 where Go answered 400.
- The port found and fixed defects that also affected Go: a cold AX resume takes about 15 s (the same as every AX deadline), which left a sandbox with a stale gateway token and a task Pending; the UI's query cache survived sign-out; a shell-exported `DOCKER_DEFAULT_PLATFORM` made images the wrong architecture.
- Two workarounds are on record: `aiomysql`'s bytes escaping is patched at import (`adapters/mysql/runs.py`), and `mcp` 2.x renamed its API (`MCPServer`, `Client`).
- Work held during the move stays in `.claude/thoughts/plans/parked/`, unedited; `docs/migration/held-work.md` says per plan what exists in Python and what must be re-planned. The feature skills were rewritten for the Python layout.
- The Go runner keeps `golang` in `.tool-versions` and in the Makefile's pinned-clone builds; nothing else needs Go.
- Decision 019's references to the Go MCP SDK now read as the Python `mcp` SDK; its choices (one adapter, audiences, limits, delegation) are unchanged.

# Alternatives and when to revisit

- **Stay in Go:** nothing was broken; rejected by the owner's choice of the team's language, not on technical grounds.
- **Python 3.15 from the start:** the key libraries did not list it; revisit when the gate is green (the gate is a make target, the pin a one-line edit).
- **Servify's sync-and-threads model:** Temporal's SDK, MCP, gRPC aio, httpx and the streaming gateway are async-native; rejected.
- **Server codegen from the OpenAPI file:** the Python generators' readiness was not verified; the contract test gives the same guarantee. Revisit if a generator proves out.
- **Porting the runner to Python:** about 1,900 lines of Go built inside AX's module with `go -overlay`, unchanged and tested; no gain. Revisit only if AX stops being built from source.
- **Resume the held plans before parity:** rejected; they were written against Go paths. Re-plan each against the Python code when the owner decides.
