---
type: "Component"
title: "Web UI"
description: "TypeScript single-page app to start runs, see what needs an answer, answer an agent's questions and read each run as a thread; the only non-Go project."
---

> Superseded in part by [decision 021](/decisions/021-python-control-plane.md): the UI is no longer "the only non-Go project": the backend is Python, the runner is the one Go program, and the UI stays the TypeScript project.

# What it is

React, TanStack Query and Vite, in `ui/`, the one project that is not Go (the owner's call: the backend is Go, the frontend is TypeScript). It talks to [gru-api](/components/gru-api.md) on its own origin; `vite dev` and `vite preview` proxy `/v1` to the API, so no CORS is needed, and a deployment needs a reverse proxy doing the same.

# Screens

Needs you (the inbox: tasks whose agent asked something, answered in place), Runs (filter by status, a badge on what needs you), a run (the thread: the agent's questions on the left, your answers on the right, status changes as quiet lines, the final answer last; the open question and a cancel button), New run (provider from `GET /v1/providers`, model required where the catalog says so; tick Conversation to keep talking: the run page then has a message box and an End conversation button, and the thread is the chat). Workflows: Workflows (the list, and a YAML editor that never parses the text itself: the server's `:validate` answers, a rejected save keeps your text, an older version opens read-only with "Restore as new version"), New run with a Workflow select (one-shot or interactive must be chosen, nothing is preselected; the Conversation checkbox is hidden), Workflow runs and a workflow run page (stages in order with each review's verdict, "2 of 3 loops", the history as a thread, the question or gate it waits on, Cancel, and Download workspace through a fetch to a blob since a link cannot carry the token). The Needs you inbox also lists workflow runs that wait, and leaves out the tasks of stages (a task with `run_id`) so a question shows once. The workflow editor has two tabs, YAML and Board. The board (React Flow, `@xyflow/react`, MIT; its attribution badge stays) draws the server's graph: a node per stage with its type, a line of its skill and the problems the server reported for it, labelled edges (`changes (up to 3 loops)`), loop edges dashed, a virtual `end` node, a minimap and controls. Editing is a draft: dragging, connecting (or the side panel's selects, which do everything without a mouse), adding and deleting stages change only the draft, the server's `:render` turns it into canonical YAML (and says what is wrong with it, on the stage), and **Apply to YAML** puts that text in the editor, with a warning first when the file has comments. The YAML is read-only and Save disabled while a draft exists; the browser asks before leaving with unsaved text or an unapplied draft. The UI never parses or writes YAML itself. Polling, not push: 2 s on a live run, 5 s on lists.

# Contract and safety

- **The generated OpenAPI document is the contract.** No file is committed: `ui/src/api/schema.d.ts` is generated from the api app (`make ui-types`, git-ignored) and a type error is the drift check.
- **Dev sign-in shortcut:** with `GRU_DEV_TOKEN_FILE` (Tilt and `make ui-dev` set it to `.cache/api-token`) the Vite dev server, and only it, adds the token to proxied `/v1` calls and answers `/__dev-auth`, so the UI skips the sign-in page; the token never reaches the browser. Production builds and `vite preview` always ask.
- **The token is in memory only** (sign-in page; a reload signs you out; a 401 from any call signs you out). Not in storage, a cookie or a URL. OIDC sign-in waits for the real issuer (D2).
- **Question and answer text is untrusted** (model output and user data) and always rendered as text; unit and browser tests try to smuggle markup in. Nothing is logged.

# Tests

The four tiers as in [testing](/components/testing.md): `make ui-unit`, `ui-integration` (jsdom against a real `gru-api --fake`, built by the global setup), `ui-acceptance` (Chromium against the built UI and the same API) and `ui-e2e` (`tests/e2e/ui`, the browser against the `gru-api` deployed in the dev cluster, with the real sandbox suspended and resumed).
