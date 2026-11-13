---
type: Decision
title: Plans are data, spend is capped per organization month, and the reconciler schedules fairly
description: An organization's limits come from a plan row with per-organization overrides, LLM spend is capped per UTC month by a third counter in the same reservation, quotas are checked where work is accepted, claiming is shared round-robin across organizations, and usage is exported to billing as numbers.
tags: [decision, multi-tenancy, budget, quotas, scheduling]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-02T18:00:00Z }
sources:
  - resource: api/gru_api/domain/use_cases/plans.py
  - resource: api/gru_api/domain/use_cases/quota.py
  - resource: api/gru_api/domain/fair_claim.py
  - resource: api/gru_api/domain/use_cases/org_usage.py
  - resource: api/gru_api/adapters/mysql/ledger.py
  - resource: docs/usage-and-limits.md
---

# Context

With organizations (multi-tenancy phase 1) every row has an owner, but spend and capacity were still global: one organization could use the whole budget, fill the pending queue or hold both workers. The next customers must be bounded and measured without anyone writing code per customer, and the owner does not yet know the numbers.

# Decision

- **Plans are data.** A `plans` row per code and an `organizations.plan_overrides` JSON; `PlanService.org_limits` merges them (an override wins, 0 is no limit) behind a 30 s cache. The seeded numbers are placeholders the owner replaces; nothing in code names a plan except `internal` for the platform organization.
- **The spend cap is a third counter in the same reservation.** Task, then run, then the organization's UTC month row, in that fixed order inside one transaction and with a conditional `UPDATE`, so a cap is never overshot and no two callers deadlock. The month is the one the call was reserved in. A refusal is the gateway's vendor-shaped 402 with its own message and counter, never retried as a budget failure.
- **Quotas are checked where work is accepted** (`Quota`), not scattered: pending at submit and run start, workflows at save, members at first sign-in, storage before a workspace export. A finished answer is never dropped for storage. Refusals reuse `LimitExceeded` (422) and are counted by quota name with no organization label.
- **Fair claiming without a MySQL-specific query.** `claim_due` reads a larger time-ordered window, `pick_fair` takes `max(1, batch / organizations with work)` per organization round-robin, and the chosen ids are locked and leased in one transaction. The spike for one statement with `ROW_NUMBER()` under `FOR UPDATE SKIP LOCKED` was not run (it needs a real MySQL); the fallback shape the plan named is correct on its own and the memory adapter applies the same rule.
- **The concurrency cap is advisory.** It is counted in `drive.submit` and a task over it stays `Pending` without a failed attempt. Under replicas it can be exceeded by at most their number; making it exact would need a lock on every start for a limit that exists to prevent monopoly, not to bill.
- **Usage is numbers for a billing system.** `GET /v1/organization/usage` for members (the per-user table for admins) and an operator export per UTC month with plan, spend, tokens, tasks, runs, storage and caps. No prices charged, no invoice, no organization name.

# Consequences

- A new plan or a changed number is an `UPDATE`, not a deploy; an operator changes one organization's limits with `PUT /v1/admin/organizations/{id}/plan-overrides`.
- The metric set grew by two series (`gru_org_spend_refusals_total`, `gru_org_quota_refusals_total{quota}`) and one setting (`GRU_CLAIM_PER_ORG`); both were owner-authorized changes to the frozen surface.
- Until the owner sets real numbers no customer should be on `starter` or `team`.
- A concurrency overshoot of a few tasks is possible and documented.
- Items not enforced yet: `max_repos`.
