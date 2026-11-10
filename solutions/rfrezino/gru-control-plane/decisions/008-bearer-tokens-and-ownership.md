---
type: "Decision"
title: "Bearer-token authentication with per-requester ownership"
description: "Callers authenticate with hashed bearer tokens behind an Authenticator seam, and every task belongs to its requester."
---

# Context

AX has no authentication, so the backend is the security boundary. Until now every caller was `anonymous` and the network policy was the only protection; the identity provider (OIDC) is not available to build against. A real gap existed: any caller could read or cancel any task by id.

# Decision

- An `Authenticator` interface in the HTTP adapter; the first implementation reads `Authorization: Bearer <token>` and compares its SHA-256 against hashes loaded from `GRU_AUTH_TOKENS_FILE` (`requester:sha256hex` lines) in constant time over every entry. A leaked file or Secret therefore does not leak usable tokens. OIDC can replace the implementation behind the same interface.
- Every `/v1` route needs a requester; `/healthz` and `/readyz` do not (probes carry no credentials). A missing or invalid token is a bare 401.
- Ownership at the use-case level (`GetOwned`, `CancelOwned`, owner-filtered lists and waits): a task owned by someone else is reported as not found (404), never 403, so ids cannot be probed. Idempotency keys are scoped per requester.
- The CLI takes the token from `$GRU_API_TOKEN` or `--api-token-file`, never from a flag value, and refuses to send it in clear text to a non-local host unless `--insecure-token` is given.
- `GRU_AUTH_MODE=none` stays for local development and warns when used outside `--fake`. The network policies remain as defence in depth.

# Consequences

- The service must still not be exposed beyond the cluster until OIDC or an equivalent is in place.
- Rotating a token means restarting the pods until a reload mechanism exists.
- Verified by tests that fail when the ownership check is removed or when any token is accepted, and in the cluster by a second requester getting 404.

Related: [gru-api](/components/gru-api.md), [deployment](/components/gru-api-deployment.md), [remaining plan](/plans/backend-service.md).
