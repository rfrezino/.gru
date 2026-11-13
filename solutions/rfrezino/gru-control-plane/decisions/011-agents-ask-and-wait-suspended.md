---
type: Decision
title: Agents can ask the user a question and wait for the answer, suspended
description: A sandbox that needs a decision reports a question through the runner, the backend stores it, suspends the sandbox to free its worker and resumes it with the answer; the agent restarts from a transcript in /workspace.
tags: [decision, agent, suspend, questions]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-15T20:00:00Z }
sources:
  - resource: api/internal/app/awaiting.go
  - resource: images/task/runner/input.go
  - resource: images/task/agent/internal/ask/ask.go
  - resource: .claude/thoughts/plans/2026-10-15-awaiting-input-and-ui.md
---

# Context

Every task was one-shot: prompt in, `answer.txt` out. A run that needs a person (approve a plan phase, pick an option, supply a fact) had no way to stop and ask, and a sandbox that waited would hold one of a fixed pool of workers, because AX has no queue. A spike on the dev cluster showed what `ax suspend` and `ax resume` keep: files under `/workspace` survive, `/tmp` and process memory do not, and the task command starts again from the top.

# Decision

- **Protocol through files and the runner.** The agent writes `/workspace/input-request.json` and waits for `/workspace/input-response.json`. The runner reports a pending question in `GET /gru/v1/result` (state `awaiting_input`) and takes the answer on `POST /gru/v1/input`, both through the router like the result.
- **The agent is restartable.** It keeps `/workspace/transcript.json`; question ids are `q1`, `q2`, ... in order, and a question already answered there is answered from the file, so a restart after a resume never asks twice. A stop signal while waiting leaves no result file.
- **The backend suspends while waiting.** The reconciler stores the question, moves the task to the non-terminal `AwaitingInput`, then suspends the sandbox (store, status, side effect, so a crash leaves something the next pass finishes). An answer wakes the task: resume, deliver, run again. Every step is idempotent. No free worker on resume is not a failure.
- **Time rules.** Waiting does not count toward `GRU_TASK_TIMEOUT` (the task keeps a `Waited` total); an unanswered question ends the task as `TimedOut` after `GRU_INPUT_TIMEOUT` (24h); cancel works in every state.
- **Everything is kept.** Questions and answers stay in MySQL as the run's history (`task_inputs`, with the status history in `task_events`), and, since Decision 015, so are prompts. Neither is ever logged.
- **API and CLI.** `GET /v1/tasks/{id}` and the list carry `pending_input`; `GET .../events` is the run as one thread; `POST .../inputs/{id}/answer` is idempotent and owner-only; the long poll returns as soon as a question is pending; `openapi.yaml` is the contract the planned TypeScript UI generates its client from, and a test keeps it exact. `gru run --api` asks in the terminal (or leaves the task waiting with exit 3), `gru inputs` and `gru answer` work on what waits.

# Conversations (D9)

A conversation is the same machinery with one more kind. `POST /v1/tasks` takes `mode: chat` (stored in `tasks.mode`, sent to the sandbox as `GRU_MODE=chat`): the prompt is the first message, each reply of the agent is a question of kind `chat` (up to 32,000 characters, longer than other questions), and the answer is your next message. The reserved answer `/end` ends it: the agent finishes with its last reply as the answer and the task Succeeds. Between turns the sandbox is suspended, so a conversation costs a worker only while a reply is being produced; the idle limit is `GRU_INPUT_TIMEOUT`, waiting does not count toward `GRU_TASK_TIMEOUT`. The agent saves the messages before every wait and a restarted agent continues them; each model call has its own timeout (a conversation has no overall one) and a model sees at most the latest 80 messages. `gru chat`, the UI's conversation view and the thread all read the same questions and answers. Because every turn suspends and resumes the sandbox, a reply takes a few seconds longer than a direct chat; a persistent sandbox per conversation is the alternative if that ever matters.

# Consequences

- The deterministic `ASK:` lines of the `echo` provider (and the fake runtime's prompt parsing) test the whole loop in every tier without a key. A model asks for real through an `ask_user` tool the agent offers whenever a user can be asked (`llm.Request.Ask`): the tool call becomes a question, the answer goes back as the tool result, and the conversation is saved in `/workspace/messages.json` before every wait so a restarted agent continues it instead of asking the model to repeat itself. Invalid or excessive tool calls go back to the model as errors, not to the user. Verified against fake OpenAI-style and Anthropic servers; not yet against a real vendor (no key).
- Every question now depends on suspend and resume: the golden-tag race and the gVisor delete wedge are on the main path (both retried or cleared as before).
- Question text is model output: clients must render it as text, and the CLI prints it on single lines.
- The direct-AX CLI path cannot take questions (no backend to suspend and resume); asking needs `--api`.
