---
type: Decision
title: Task image hardening is a proposed spike, not done; no image change yet
description: What to try to harden the task image (non-root agent, read-only root, dropped capabilities, fewer tools), in what order, and why nothing was changed in the workspace-transfer phase.
tags: [decision, security, image, sandbox, proposed]
status: draft
generated: { by: claude-code/sonnet-5.5, at: 2026-10-15T23:30:00Z }
sources:
  - resource: images/task/Dockerfile
  - resource: images/task/runner/workspace.go
  - resource: .claude/thoughts/plans/2026-10-15-workflows-phase-4-workspace-transfer.md
  - resource: .claude/thoughts/investigations/2026-10-15-user-composed-workflows.md
---

# Status

**Proposed. The spike has not been run and the image is unchanged.** Changing the task image's user, filesystem or capabilities is an ask-first item in the workflows spec, and the experiments need the dev cluster and a day of trial. This note records what is known and what to try, so the owner can approve the spike and its outcome can be written here.

# What is known

- The task image runs everything as root: `images/task/Dockerfile` has no `USER`. The runner is PID 1 (the entrypoint AX requires), binds port 80 and starts the task command; the agent (`gru-agent`) is that command.
- The agent's file tools are confined to `/workspace/work` with `os.Root` ([decision 012](/decisions/012-agents-work-on-files-in-a-confined-directory.md)) and have no shell or network, and the workspace archive is unpacked by a hardened extractor ([task image](/components/task-image.md)). gVisor is the outer boundary of the sandbox.
- The image still ships `curl`, `git`, `procps` and `bash`; nothing in gru runs them.

# Experiments, in order, each checked with `make ask BACKEND=echo PROMPT=test`

1. A non-root user for the agent process only. First confirm whether the stock AX runner can drop privileges for the child it spawns; the runner itself must stay able to bind port 80.
2. A read-only root filesystem with `/workspace` and `/tmp` writable.
3. Dropped Linux capabilities.
4. Removing `curl`, `git` and `procps` from the image.

# What to record when it is done

What worked, what the AX runner or Substrate does not allow, and what stays as it is because gVisor is the boundary. An outcome of "nothing feasible; no image change" is acceptable.
