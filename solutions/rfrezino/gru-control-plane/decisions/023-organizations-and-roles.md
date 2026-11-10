---
type: "Decision"
title: "Organizations, roles and the web surface"
description: "Roles live in gru and are checked in the use cases, another organization's rows are 404, API-key secrets are shown once and never travel over MCP, GitHub connects through a signed state, and the UI gates its links by the role the server reports."
---

# Context

Phase 1 gave every row an owner and a `Principal`; phases 2 and 3 made settings, hooks, GitHub, code search, plans and spend per organization. People still could not manage any of it: there was no way to list members, make an API key, read the plan or connect GitHub without an operator, and the UI could not even say who was signed in.

# Decision

- **Roles live in gru, not in the token.** `memberships.role` is `member` < `admin` < `owner`; `operator` is a platform role that never satisfies an organization role check on its own. The roles are revocable at once, and an identity provider's own roles differ per provider. The use cases check the role (`Role.at_least`); the routers only authenticate, so a route cannot forget a check the use case makes anyway.
- **Another organization's rows are 404, a missing role is 403.** A member who calls an admin route learns that it exists; a person who names another organization's member, key or installation learns nothing.
- **The last owner cannot be demoted or removed** (422), and only an owner (or an operator) makes an owner. An organization is never left without someone who can fix it.
- **An API key's secret exists once.** `gru_k_<8 hex>_<64 hex>` comes from an `ISecretGenerator` port (so `secrets` stays out of `domain/`), only the SHA-256 is stored, and the secret is in the answer of the create call and nowhere else: not in the list, not in a log, not in an audit line. **There is no MCP tool for keys**, because a key returned through MCP lands in a model's transcript; member removal and organization deletion are not MCP tools either. Seven organization tools exist (five for people, two for organization admins through a new `ORGADMIN` audience) and a sandbox never gets any of them.
- **GitHub connects through a state gru signs, not through an `installation_id` it was told.** The install link carries a state bound to the organization, the user and ten minutes, usable once; the return page sends `installation_id`, `state` and `code` in a body, and gru confirms with GitHub both that this person can reach the installation and that the App is installed there before it maps it. An installation another organization holds is refused (409). The return page removes the values from the address at once, so they are not in history, a bookmark or a log.
- **The UI gates links by the role the server reports, once.** `GET /v1/organization` returns the caller's organization and role; the sidebar and the administrator pages use it, not a probe for a 403 (the old Hooks check). A member who opens an administrator's address gets a clear message and no request is made. The server still decides every request.
- **Nothing here is billing.** Members see usage totals, administrators see each person's share, and the operator's export is the hand-off to a billing system (decision 022).

# Consequences

- A customer's administrators can run their organization without an operator, apart from creating it and setting its plan.
- Routes, MCP tool names, metric series (`gru_authz_denied_total{reason}`, `gru_orgs{status}`) and `GRU_GITHUB_OAUTH_*`, `GRU_GITHUB_WEB_URL` were added with the owner's explicit approval; nothing existing changed.
- A person is in one organization per sign-in; there is no switcher.
- The identity provider (the default WorkOS) is a procurement decision, not code; the OIDC seam does not change.
- The admin links disappear for a server that cannot say the role (`getOrganization` fails): the UI offers the member's view, never the administrator's.
