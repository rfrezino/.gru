---
type: Playbook
title: Local development workflow
description: Bring the dev environment up, run a prompt, and tear it down.
tags: [runbook, dev, make]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-10-14T19:00:00Z }
sources:
  - resource: README.md
---

# Prerequisites

Docker Desktop running; `kubectl`, `k3d`, `tilt`; Python via uv and Go via asdf (`.tool-versions`; Go only builds the task runner), Node 24+ for the UI.

# Steps

1. `make up` (about 10 minutes from scratch).
2. `make tilt-up` deploys gru-api and the UI; `make api-cluster-test` proves the whole path; its metered tests need a real Claude key saved by the dev organization's owner and spend real money (the echo backend needs none).
3. Real model: no vendor key is read from the shell or `.env` any more. An owner (or a Gru admin) of the organization saves a Claude API key on the Claude API key settings page (`/settings/claude-key`); `make api-up` has already created the dev KMS key in floci that wraps it (floci loses it when its pod restarts, then enter the key again). Then start a task with `claude-code` (the default) through the API, the UI or MCP. There is no fake vendor ([decision 033](/decisions/033-no-fake-vendor.md)): a real key is needed to generate, and without an Anthropic account only `--fake` and the unit tests run the metered path. With no key a task fails with a message saying no model vendor is enabled; with a rejected key, that the key is invalid or revoked.
4. Iterate: `make tilt-up` rebuilds the task image and gru-api on change; `make api-unit` runs the API unit tests and `make check` every test that needs no cluster ([testing](/components/testing.md)).
5. `make down` when finished (`make reset` rebuilds from scratch).

# Through the backend

1. `make db-up` (the Cloud SQL instance in [floci](/components/floci.md)), then `make api-dev` (runs [gru-api](/components/gru-api.md) locally, auth off) and, in another shell, `make ask-api BACKEND=echo PROMPT="hi"`.
2. Or in the cluster: `make api-image api-up` , `kubectl -n gru-system port-forward svc/gru-api 8080:8080`, then `make ask-api ...`. `make api-up` writes two dev tokens to `.cache/api-token` and `.cache/api-token-other`; `make ask-api` uses the first. `make api-cluster-test` checks the deployment ([details](/components/gru-api-deployment.md)).
3. Do not stop `make api-e2e` midway: it can leave actors that hold the workers (see [troubleshooting](/runbooks/troubleshooting.md)).

# Rules

Never print or commit key values; do not read `.env`. After restarting the k3d node container, rerun `make net-fix`. See [dev environment](/components/dev-environment.md) and [troubleshooting](/runbooks/troubleshooting.md).
