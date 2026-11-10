---
type: "Decision"
title: "GKE identity, egress and data handling"
description: "Who may call gru-api (Google identities through IAP for people, projected Kubernetes service account tokens for services, no static tokens), how traffic leaves the cluster (a sandbox reaches only gru-api; the control plane leaves through Cloud NAT and FQDN policy), and how long prompts, answers and work directories are kept (encrypted with CMEK, short fixed retention, purged by a job)."
---

# Context

The owner asked for D2 (identity), D4 (GKE egress) and D6 (data classification and retention) to be chosen with the best security practice and the GKE-native approach. Confirmed by the owner on 2026-10-22: the identity provider is Google Workspace, the retention numbers stand, and no separate security review is required. Each choice says what would change it.

# Decision

## D2: identity

- **People: Google identities through Identity-Aware Proxy** in front of the GKE Gateway. IAP authenticates before a request reaches the cluster, enforces who may use the app (an IAM group, not a list in gru), and adds a signed `x-goog-iap-jwt-assertion`. gru-api verifies that JWT itself (issuer `https://cloud.google.com/iap`, the audience of the backend service, the signature against Google's published keys, expiry) and takes `email` as the requester. IAP alone is never trusted: a request without a valid assertion is refused, so a path that bypasses the proxy fails closed. The OIDC authenticator already built (`GRU_AUTH_MODE=oidc`) stays for any other IdP; IAP verification is a second authenticator behind the same seam (no `go-oidc` discovery: IAP publishes a key set, not a discovery document).
- **Services: projected Kubernetes service account tokens**, audience `gru-api`, short lived and rotated by the kubelet. gru-api verifies them against the cluster's OIDC issuer (GKE publishes one per cluster); the requester is the token's `sub` (`system:serviceaccount:<namespace>:<name>`). No static bearer token on GKE: the token file stays a dev and CI-outside-the-cluster path and must be empty in the GKE overlay (a test says so).
- **GCP access from pods is Workload Identity Federation for GKE**, one Google service account per component with only what it needs (Cloud SQL client, the snapshot bucket, KMS for the App key). No key files.
- Ownership by requester is unchanged. If the organisation's IdP is not Google Workspace, replace IAP by the organisation's OIDC provider through the existing authenticator; nothing else here changes.

## D4: egress

- **A sandbox reaches nothing but gru-api.** The LLM goes through the gateway and code search through the gateway listener, so a sandbox needs no internet. In GKE the egress rule is deny-all plus one allow, gru-api's gateway port (Substrate `EgressPolicy` for the actor traffic, and a Kubernetes NetworkPolicy for the pods). The dev workaround `make egress-direct` does not exist on GKE.
- **The control plane leaves through a fixed path.** GKE Dataplane V2 with FQDN network policies for gru-api (`api.github.com`, the vendors' API hosts, nothing else), Cloud NAT with reserved static addresses so a vendor or GitHub can allow-list them, and private IP for Cloud SQL (Private Service Connect or private services access), so the database has no public address.
- **MTU is verified, not assumed.** GKE's default pod MTU (1460) is below the sandbox's 1500, the same shape as the dev TCP-stall bug. The first GKE test is a TLS call from a sandbox through the gateway; the fix, if needed, is an MTU or MSS setting in the cluster, never in the sandbox image.

## D6: data

- **Classification:** prompts, answers, questions, work directories and run history are *confidential*: they may carry source code and whatever people type. They must not carry customer financial data or personal information of FreshBooks customers until a DLP step exists; the UI and CLI say so next to the prompt box, and the answer is the user's, not ours, to scrub.
- **Encryption:** Cloud SQL and the snapshot bucket use customer-managed keys (CMEK) in Cloud KMS; TLS to the database is required in the DSN.
- **Retention (defaults, one place each):** work directories and results `GRU_RESULT_TTL` and `GRU_RUN_RETENTION` 7 days (today 24h); prompts, answers and questions of finished tasks and runs 90 days, then a purge job deletes the row's text and keeps its metadata (ids, status, cost, timestamps) for 13 months for the ledger; the usage ledger has no text. A requester can delete their own runs (an endpoint, later). Shorter is allowed per deployment; longer needs a reason written here.
- **Never:** logged, used as a metric label, or sent anywhere but the chosen provider (Decision 015 stays).

# Consequences

- IAP needs an OAuth consent screen and a backend service, so the Gateway/Ingress and a Google group must exist before the GKE overlay is usable by people; until then a port-forward with a dev token is the only way in, as today.
- Cluster-issued tokens tie service identity to the cluster: a second cluster needs its issuer added.
- The retention purge job and the 90-day default are new code and a new config pair; Decision 015's "no retention job" is replaced by it.
- FQDN policy needs Dataplane V2; if the cluster is not created with it, egress for the control plane falls back to Cloud NAT plus a firewall allow-list by address range, which is weaker.

# Alternatives and when to revisit

- **Static tokens in a Secret for services:** simplest, but long-lived and copyable; kept only for dev.
- **Identity-aware proxy of our own (oauth2-proxy):** portable, but we would run and patch it; revisit if the organisation is not on Google identities.
- **Substrate credential injection or AX secret references** for provider keys: not chosen (D5 is the gateway).
- **Keep prompts forever:** useful for review, but unbounded confidential data at rest; revisit with a data owner's retention rule.
