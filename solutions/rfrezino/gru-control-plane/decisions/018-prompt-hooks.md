---
type: "Decision"
title: "Prompt hooks are operator policy, run by an agent task of their own"
description: "The operator can add instructions to the agent's system message, refuse requests by pattern, and have a model rewrite the request before the agent starts; hooks apply to every task and run, fail closed, and keep the user's text beside the rewrite."
---

# Context

The owner wanted a step between "I want to do this" and the agent that does it, to transform messages, improve them and add ad hoc documentation. First answers chose operator policy, fail closed, and declarative built-ins; the owner then said the transformation itself is done by an agent, which added the `agent` kind.

# Decision

- **Operator policy, one file** (`GRU_HOOKS_FILE`), at most three hooks, applied to every task and run. Users cannot switch them off or add their own.
- **Two kinds.** `instructions` (no model: system-message text with a few facts, and `block_if` patterns that refuse a request with a 400 naming only the hook) and `agent` (a model rewrites the request).
- **An agent hook is a task of its own**, not a control-plane call: the sandbox has no tools or network, the gateway meters it, it has its own task budget and a mandatory cap, and it reuses the runtime and result channel. A plain task is driven by the reconciler without waiting; a run gets one guarded engine step before the first stage (`workflow.GetVersion` keeps recorded histories replaying).
- **The user's text is never overwritten.** `Task.Prompt` and `Run.Prompt` stay as typed; `EffectivePrompt` is kept beside them, shown only on single GETs, and falls under the same retention (decision 017).
- **Fail closed.** A hook that errors, times out, returns nothing or too much, or overruns its budget fails the work; a composed system message the agent would not accept fails it too. Nothing is silently cut or skipped.
- **Trust.** An agent hook's output is user-level text; only the operator's `instructions` text reaches the system message.
- Provider checks run at start: the hook's provider must be enabled, through the gateway, and have a price for its model.

# Consequences

- A task with agent hooks costs a sandbox start per hook before it begins.
- A hook's spend has its own cap and is not deducted from a run's budget or shown in its usage view; a run can spend its budget plus its hooks' caps.
- Conversations and the answers to agent questions bypass hooks (a gap for enforcement, stated in `docs/hooks.md`).
- The recorded Temporal histories do not contain the new step; a new one needs a real Temporal server.

# Alternatives and when to revisit

- **Hooks in the workflow YAML:** author-chosen, no org-wide enforcement; revisit for per-workflow hooks on top of this.
- **A webhook to a service we run:** arbitrary transformation, but it sends user text to another service; revisit if built-ins and the agent kind are not enough.
- **Running user code (WASM or scripts):** largest security surface; not started.
