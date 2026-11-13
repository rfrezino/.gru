---
type: Decision
title: A stage declares the files it keeps, every task saves its conversation, and a run's files download as bytes or tar.gz
description: "Workflow stages may list `keep:` paths copied into artifacts by the agent, each task writes a conversation.json the UI renders, a failed step stores its reason, and the work directory's files download from one route."
tags: [decision, artifacts, workflows, conversation, ui]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-05T12:00:00Z }
sources:
  - resource: api/gru_api/domain/use_cases/artifact_pull.py
  - resource: api/gru_api/domain/run_download.py
  - resource: api/gru_api/adapters/ax/taskspec.py
  - resource: images/task/agent/gru_agent/domain/use_cases/keep.py
  - resource: images/task/agent/gru_agent/domain/conversation.py
  - resource: ui/src/board/RunFlow.tsx
  - resource: ui/src/components/ConversationCard.tsx
---

# Context

Decision [027](/decisions/027-run-artifacts.md) keeps only what an agent writes under `artifacts/`. In practice a plan stage wrote `plan.md` in the work directory root and a stub under `artifacts/`, so the real plan was never kept; the model's reasoning and tool calls died with the sandbox; a failed step showed only "Failed"; and the run page sent people to a separate task page to see any of it.

# Decision

- **`keep:` per stage.** A workflow stage lists up to 20 work-directory paths (artifact naming rules, no duplicates; refused on review and approval-only stages). When the stage ends with an answer the agent copies each `work/<path>` to `work/artifacts/<path>` (declared file wins, confined with `realpath` and `O_NOFOLLOW`, same 1 MiB cap). A missing file fails the stage with `the stage did not write <path>, which it must keep`. Transport is the task variable `GRU_KEEP_FILES` (with `GRU_STAGE_ID`), set only when declared. `keep` lives on the compiled `Unit`, not on `Plan`, so the recorded Temporal histories still replay.
- **Inherited files are dropped.** The work directory is shared and cumulative, so a file whose name and SHA-256 already belong to an earlier task of the same run is not stored again for a later task.
- **Every task saves its conversation.** At the end of a task (answer or failure, never changing the outcome) the agent writes `work/artifacts/conversation.json`, version 1: user, assistant, thinking, tool_use and tool_result entries, each clipped to 4096 characters, redacted with the task key, first 10 plus the latest entries when over 900 KiB. Claude Code sessions are read from the JSONL under its home; the plain agent hands over its message list. `conversation.json` is a reserved name. Extended thinking is not turned on; only reasoning the source already has is recorded.
- **Failure reasons are stored.** Migration `0013` adds a nullable `error` to run steps, exposed as `steps[].error` (omitted when empty) and in the failed `stage_finished` detail.
- **One download route.** `GET /v1/workflow-runs/{id}/files/download?path=...` returns one file's exact bytes or a deterministic `tar.gz` (a folder, several paths, the root), with `X-Gru-Archive-Sha256`; at most 500 paths, 413 over the archive cap, 404 if any path is missing.
- **The run page is the task view.** One Flow card holds the diagram, a step panel with Overview, Conversation, Artifacts and Files tabs (`#step-<id>&tab=…&run=<seq>`) and a resizable, collapsible Activity sidebar; the approval card embeds the file reader so the plan is on screen when deciding. Syntax highlighting uses `lowlight`, rendered to React nodes with no raw HTML.
- **Silent models.** A blank final reply in the plain loop is nudged once; a second blank fails with the stop reason. The 1024-token reply cap is gone (default `max_tokens` 16000).
- **Approved surface (additive).** One route, `steps[].error`, variables `GRU_KEEP_FILES` and `GRU_STAGE_ID`, migrations `0013` and `0014` (seeded repository limits off).

# Consequences

- Kept files are explicit in the workflow YAML; stored workflows such as `fresh-flow` get `keep:` only when their owner edits them.
- A conversation costs one artifact per task; an oversize one is cut rather than risking the real artifacts.
- Not built: a diff of changed files at approval, sandbox log capture (AX returns no stdout), MCP tools for the download. The session reader is written from documented shapes and has not been run against a real Claude Code session.
