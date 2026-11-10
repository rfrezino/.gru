---
type: "Playbook"
title: "Local development workflow"
description: "Bring the dev environment up, run a prompt, and tear it down."
---

# Prerequisites

Docker Desktop running; `kubectl`, `k3d`, `tilt`; Go via asdf (`.tool-versions`).

# Steps

1. `make up` (about 10 minutes from scratch).
2. `make tilt-up` deploys gru-api and the UI; `make api-cluster-test` proves the whole path without a key (the echo backend).
3. Real model: export the key in your own shell or put it in a gitignored `.env` before `make api-up`, then start a task with the `claude` backend through the API, the UI or MCP.
4. Iterate: `make tilt-up` rebuilds the task image and gru-api on change; `make api-unit` runs the API unit tests and `make check` every test that needs no cluster ([testing](/components/testing.md)).
5. `make down` when finished (`make reset` rebuilds from scratch).

# Through the backend

1. `make db-up` (the Cloud SQL instance in [floci](/components/floci.md)), then `make api-dev` (runs [gru-api](/components/gru-api.md) locally, auth off) and, in another shell, `make ask-api BACKEND=echo PROMPT="hi"`.
2. Or in the cluster: `make api-image api-up` (add `GRU_API_KEYS=none` to deploy without provider keys), `kubectl -n gru-system port-forward svc/gru-api 8080:8080`, then `make ask-api ...`. `make api-up` writes two dev tokens to `.cache/api-token` and `.cache/api-token-other`; `make ask-api` uses the first. `make api-cluster-test` checks the deployment ([details](/components/gru-api-deployment.md)).
3. Do not stop `make api-e2e` midway: it can leave actors that hold the workers (see [troubleshooting](/runbooks/troubleshooting.md)).

# Rules

Never print or commit key values; do not read `.env`. After restarting the k3d node container, rerun `make net-fix`. See [dev environment](/components/dev-environment.md) and [troubleshooting](/runbooks/troubleshooting.md).
