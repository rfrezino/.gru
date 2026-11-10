---
type: "Playbook"
title: "Troubleshooting"
description: "Symptoms seen while building this stack, their causes and fixes."
---

| Symptom | Cause | Fix |
|---|---|---|
| `kubelet`/`journald` fail, kubeadm init times out (kind, vind) | systemd in the node on this Docker Desktop VM | use k3d ([decision](/decisions/001-k3d-instead-of-kind.md)) |
| `Bind for 127.0.0.1:5001 failed` creating the cluster | an old `kind-registry` container holds the port | `docker rm -f kind-registry` |
| `filesystem on .../upper not supported as upperdir` | hostPath on an overlay2 root | Docker volumes in `dev/k3d.yaml` for `/var/lib/ate` and `/var/lib/ateom-gvisor` |
| `lookup kind-registry ... no such host` | registry not named `kind-registry` | name it so in `dev/k3d.yaml` |
| `GoldenActorCrashed` for the counter demo | usually one of the two rows above | fix them, delete the demo (`--delete-demo-counter`), redeploy |
| `snapshot_config: unknown field`, `actor template not found` | Substrate newer than AX's dependency | [pin](/decisions/002-pin-substrate-to-ax-dependency.md) and `make reset` |
| `no free workers available` | no WorkerPool, workers not registered, or all busy | `make workers-up`; after atelet restarts, restart worker pods |
| image pull `DENIED` from `gcr.io/ax-substrate` | default task image is not public | use `make task-image` and `localhost:5001/...@sha256` |
| `a Golden data resume requires the ActorTemplate golden tag` | suspend/resume right after task creation | wait for the golden snapshot (a few seconds) |
| `APIConnectionError` from the agent; HTTPS timeouts | tunneled egress and/or MTU | `make egress-direct net-fix`, then restart workers ([decision](/decisions/004-direct-egress-and-mss-clamp-in-dev.md)) |
| `OPENAI_API_KEY is not set` | not exported and `.env` lacks it | export it or add it to `.env` |
| `--model is required for the openai backend` | no default model on purpose | pass `MODEL=` |
| floci pod crash-loops with a port parse error | service-link env var collision | `enableServiceLinks: false` |
| AX or Substrate commands hit the wrong cluster | kube context | contexts are `k3d-gru`; `make` passes `KUBECTL_CONTEXT` |
| every new task stays `Pending`; AX phase `Failed`; `kubectl-ate --context k3d-gru get workers` shows `1/1` actors with nothing running | actors left `RUNNING` or stuck `DELETING` by an interrupted e2e or CLI run hold both workers | `kubectl-ate --context k3d-gru delete actor --atespace default --any-state <name>` for each (list with `get actors -A`); stranded tasks then finish on their own |
| `401 unauthenticated` from gru-api | no token, or one the server does not know | set `GRU_API_TOKEN` or `--api-token-file .cache/api-token` (written by `make api-up`) |
| the cluster tests print `missing .cache/api-token: run `make api-up` first`, or a call to the API gets 401 | the dev tokens were never generated | `make db-up api-image api-up` |
| a new pod cannot reach gru-api for a few seconds, then can | the NetworkPolicy engine learns new pods late | retry; the cluster tests already do |
| `--api` with a token to a remote `http://` host is refused | the CLI will not send a token in clear text to a non-local host | use `https://`, or `--insecure-token` on a trusted network |

# Leaked `gru-*` tasks

On the direct-AX path `gru` deletes its task on success, error, timeout, SIGINT and SIGTERM. A SIGKILL, a crash or `--keep` can leave one behind, and it holds a worker. Tasks created through the API are named `gru-api-<id>` and are cleaned up by the service ([decision 007](/decisions/007-both-cli-paths-and-own-sandbox-prefix.md)); do not delete those by hand while `gru-api` is running unless they are stuck. From the repo root:

```bash
bin/ax get tasks                      # list; leftovers are named gru-<date>-<time>-<id>
bin/ax delete task <name>             # delete one
bin/ax get tasks | awk 'NR>1 && $1 ~ /^gru-/ {print $1}' | xargs -n1 bin/ax delete task   # delete all gru-* tasks
```

A `gru gc` command is deliberately not built; add one only if leaks keep happening.

# Checks and what the failure messages mean

- `make check` runs the offline tests; `make api-cluster-test` checks the deployed gru-api on the dev cluster (echo backend, no key).
- `timed out after <d>: waiting for task to run | waiting for answer`: nothing finished in `--timeout`; the task is deleted. Raise `--timeout` or check `make cluster-status`.
- `no free worker` (task failed after resume was refused): the WorkerPool (2 workers) is busy. AX has no queue and marks the task `Failed`; wait and re-run, or scale `deploy/workers/workerpool.yaml`.
- `agent error: AuthenticationError ...`: the provider rejected the key.
- `agent error: APIConnectionError ... (cannot reach the provider ...)`: the sandbox cannot reach the provider; run `make egress-direct net-fix` and restart workers. The agent gives up after `GRU_HTTP_TIMEOUT` (120 s per model call). A message about the provider not answering within the time limit is ambiguous: it is a blocked network, or a slow model (reasoning models and long conversations can exceed a short limit; in a conversation each turn is a new call).

