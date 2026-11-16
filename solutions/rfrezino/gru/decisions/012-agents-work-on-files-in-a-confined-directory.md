---
type: Decision
title: Workflow stage agents work on files in a confined directory, with narrow tools and no shell
description: A workflow stage's agent gets read, list, search, write and edit tools rooted at /workspace/work, a review stage only reads and submits a structured verdict, and there is no shell or network tool.
tags: [decision, agent, tools, security, workflows]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-15T23:00:00Z }
sources:
  - resource: images/task/agent/internal/tools/files.go
  - resource: images/task/agent/internal/tools/box.go
  - resource: images/task/agent/internal/tools/verdict.go
  - resource: images/task/agent/internal/llm/anyllm.go
  - resource: .claude/thoughts/investigations/2026-10-15-user-composed-workflows.md
  - resource: .claude/thoughts/plans/2026-10-15-workflows-phase-3-stage-agent.md
---

# Context

Workflow stages (build, review, ...) need to produce and inspect files, and stages hand their work to each other. Until now the agent could only call a model and ask a question. A generic shell tool would let a model run anything, and a tool over the whole of `/workspace` could overwrite the agent's own control files (`answer.txt`, `error.txt`, `transcript.json`, `messages.json`, `verdict.json`) and forge a result.

# Decision

- **Narrow tools, no shell, no network.** `read_file`, `list_dir`, `search_text`, `write_file` and `edit_file` (an exact, unique match). Nothing else; no tool starts a process or opens a connection.
- **Rooted at `/workspace/work`.** Every access goes through Go's `os.Root`, which refuses `..` and symlinks that leave the root; absolute paths, `..`, empty parts, names starting `.gru` and paths over 255 characters or 16 levels are refused before that. The control files live in `/workspace`, out of reach.
- **Bounded.** A file is at most 1 MiB, the directory 16 MiB and 2000 files, a list 500 entries, a search 100 matches, a tool result 32 KiB. Writes go through a temporary file and a rename, mode 0644; the tools create no symlinks, special files or executables.
- **Least privilege by stage.** A `review` stage has the read tools and `submit_verdict`, and its file handle is read-only; an `agent` stage has the file tools. `ask_user` is added only when someone can answer (`GRU_INTERACTIVE` is not `0`); in a one-shot run it is not offered and a call to it gets "unknown tool".
- **The verdict is a tool call.** `submit_verdict(approved, summary, changes[])` is validated and written atomically to `/workspace/verdict.json`; a review that ends without one fails with "review finished without a verdict". The loop cap and the decision to loop live in `gru-api`, never in the sandbox.
- **Restartable.** The conversation, including the stage's system message (`GRU_SYSTEM`), is saved before every step. A replayed `edit_file` whose change is already there succeeds, so a restart between running a tool and saving its result is harmless.
- **Nothing is logged.** The agent prints nothing; tool arguments and results appear only in `messages.json`. Errors shown to the model are short and never contain a host path.

# Consequences

- The agent cannot build or test code yet; a shell or run-tests tool would be a new decision and needs an egress and sandbox review.
- gVisor remains the outer boundary; the tools are the inner one. Prompt injection in a file is data: the worst it can do is bounded to the work directory.
- The `echo` provider scripts the tools (`WRITE`, `EDIT`, `READ`, `LIST`, `VERDICT ...`) through the same dispatch a model's calls use, so every test tier covers this without a key.
