---
type: Decision
title: Gru staff live in the database, act in any organization through a header, and every change is audited; admins can lend members scoped, expiring grants
description: "Operators are rows in platform_staff (the env list only seeds an empty table); an operator acts as an owner of a customer organization per request with X-Gru-Act-As-Org; every mutation is an append-only audit event of ids and field names that survives a purge; an admin can lend a member write access to named areas for up to 30 days."
tags: [decision, organizations, audit, staff, permissions]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-03T23:00:00Z }
sources:
  - resource: api/gru_api/domain/use_cases/audit.py
  - resource: api/gru_api/domain/use_cases/platform_staff.py
  - resource: api/gru_api/domain/use_cases/acting.py
  - resource: api/gru_api/domain/use_cases/member_grants.py
  - resource: api/gru_api/host/http/acting.py
  - resource: api/gru_api/host/http/grants.py
  - resource: docs/admin-panel.md
  - resource: docs/organizations.md
  - resource: .claude/thoughts/investigations/2026-11-03-platform-staff-admin-panel-and-audit.md
---

# Context

gru is a SaaS run by Gru Inc. Gru's own staff must see and manage every customer organization and act in it for support; customers want an accountable trail of every change, including by staff, and want to lend a member a narrow permission without promoting them for good. The platform role `operator` already existed (a frozen list from `GRU_PLATFORM_OPERATORS`, effective only in the platform organization).

# Decision

- **Staff are rows.** `platform_staff` replaces the frozen list; `GRU_PLATFORM_OPERATORS` only seeds an empty table at start, so a removal is never undone by the variable. Only an operator manages staff and the last operator cannot be removed (422). The role stays named `operator` in code and docs; the UI says "Gru staff".
- **Acting is a per-request header, not a session.** `X-Gru-Act-As-Org: <organization id>` from an operator who is still staff yields an owner of that organization named by the operator's own user id (`Principal.acted_by`); a non-operator gets 404, API keys ignore the header, sandboxes and the gateway never see it. Stateless: nothing to expire or leak. A suspended or deleting organization can be entered for support; a deleted one cannot. The acting principal is not an operator, so `/v1/admin/*` is closed to it. Entering is audited once per (operator, organization, hour) per replica; every change is audited on its own.
- **One audit port, written by use cases.** `audit_events` is append-only (the port has `append` and `list`), global, and records who (the real person, and whether person, API key, operator or system), the organization, a fixed action, a target id, the names of the fields and whether it came over HTTP or MCP. No value, name, email, prompt or key. A failed append fails the call (closed). It is written after the change succeeds, not in its transaction.
- **Audit survives purge and keeps user ids.** Accountability over erasure; the rows hold ids only. Revisit if a customer's contract requires erasing ids.
- **Grants are checked per request and never reach roles.** An admin lends a plain member write access to areas (`settings`, `hooks`, `github`, `members`, `api_keys`, `workflows_admin`) until a time (24 h by default, 30 days at most), one row per area, revocable at any moment. The active grants are loaded onto the member's principal on every request, so expiry and revoke are immediate; `Principal.may_admin(area)` is the one check. A grant does not change roles (a member could promote themselves): `members` lets a holder remove plain members only, `api_keys` lets a holder make member keys only, a holder cannot grant and cannot read the audit trail. API keys, services and sandboxes never carry grants.
- **Staff reach gru through the platform connection**, and the identity provider must enforce MFA for it: the code cannot check this, the runbook does.
- **Approved surface (additive).** Routes `/v1/admin/staff*`, `/v1/admin/audit`, `/v1/organization/audit`, `/v1/organization/member-grants*`, optional `grants` on `GET /v1/organization`; MCP tools `list_member_grants`, `grant_member`, `revoke_member_grant`, `list_audit` (52 names); series `gru_audit_events_total{action}` and `gru_member_grants_total{result}` (49 series); migrations `0007_audit`, `0008_platform_staff`, `0009_member_grants`; the `/admin` panel and Settings "Access".

# Consequences

- A staff member acting in an organization has owner power there, always audited; there are no sub-tiers of staff and no impersonation of a specific customer user.
- The last-operator check is count-then-remove (not atomic); an empty table is re-seeded from the variable at the next start.
- The entry audit is deduplicated in process: with several replicas an operator may produce one entry per replica per hour.
- Not built: audit export or retention, read logging of normal work, multi-organization sessions for customers, SCIM.
