---
type: Decision
title: Solutions Overview is the first backed-in feature, built on a generic scheduler, a visible run origin and an org_features registry
description: "An organization switches on Solutions Overview and gru keeps an OKF bundle and graph of each chosen repository; it stands on a leased scheduler with a customer schedule: field, an origin on runs and tasks that every member can read, and a generic per-organization feature table."
tags: [decision, solutions-overview, scheduler, origin, features, okf, gitops, grants]
status: stable
generated: { by: claude-code/sonnet-5.5, at: 2026-11-06T23:30:00Z }
sources:
  - resource: .claude/thoughts/investigations/2026-11-06-solutions-overview.md
  - resource: api/gru_api/domain/features.py
  - resource: api/gru_api/domain/use_cases/solutions.py
  - resource: api/gru_api/domain/use_cases/solutions_refresh.py
  - resource: api/gru_api/domain/use_cases/scheduler.py
  - resource: api/gru_api/domain/use_cases/schedule_sync.py
  - resource: api/gru_api/domain/workflow/cron.py
  - resource: api/gru_api/domain/workflow/schedule.py
  - resource: api/gru_api/domain/solutions/bundle.py
  - resource: api/gru_api/domain/solutions/graph.py
  - resource: api/gru_api/host/http/routers/solutions.py
  - resource: api/gru_api/adapters/mysql/migrations/versions/0025_solutions_foundation.py
  - resource: docs/solutions-overview.md
  - resource: docs/workflows.md
---

# Context

The owner wanted every organization to get an always-current, browsable map of its repositories with no setup beyond a toggle and a repository pick, and customers wanted recurring work of their own. Both need something that starts runs by itself, and a run nobody typed raises a trust problem: the organization pays for it, so it must be visible and explained. The next backed-in feature should reuse the same structure rather than invent its own.

# Decision

1. **One generic scheduler with two kinds of target.** A `schedules` table, a leased `schedule-loop` in every replica (`FOR UPDATE SKIP LOCKED`, the GitOps `claim_due` shape) and targets `workflow` (a normal run) or `job` (a registered in-code job such as `solutions.refresh`, which can decide before any run is paid for). Rejected: a private seven-day loop for Solutions, which would have been a second mechanism to retire.
2. **Runs started by gru are visible and labelled, not hidden.** `Run.origin` and `Task.origin` (`system:solutions-overview`, `schedule:<workflow>`; omitted from JSON when empty so the pinned golden bodies stay). Work with an origin is readable by every member (lists, run page, usage, MCP text) with a label; cancel, retry and answer stay with the creator or an admin. Customer impact: no unexplained spend and no invisible runs.
3. **Who a run starts as.** Solutions Overview runs as `Principal.system` (a `MEMBER`/`SERVICE`, `system:solutions-overview`): it skips only the per-requester allow-list check; the repository must still be usable in the organization's allow-list, selected by an admin, and the organization's Claude key and limits apply. A customer schedule runs as the workflow's last saver, a person, with every normal check; a schedule saved by a service, an API key, a task or GitOps is refused ("a scheduled workflow must be saved by a person"), so a schedule never grants its saver access they lack.
4. **How a bundle leaves the sandbox.** The agent writes `.okf/` in the run's work directory; the stage export stores the workspace; the collector reads it before the 24-hour retention ends, keeps `.okf/**/*.md` and replaces the repository's `solution_docs` as a set in one transaction (no half bundle is ever visible).
5. **The sha rule and pinning.** The refresh reads the live head with `IRepoHost.head_commit`, compares it with `generated_commit` (equal: `up_to_date`, nothing started unless `force`), and pins the run with `ref=<that sha>` so the stored sha is exactly what was documented. The code-search mirror is not used to decide.
6. **Cost bounds.** A per-repository budget (`budget_usd`, default 2.00, capped by the platform ceiling at save and by the organization's own cap at start), at most three generation runs at once per organization (checked per collector pass, so replicas ticking together can briefly exceed it), the plan limit `max_solution_repos` (50) and the organization's Claude key required.
7. **Cron is parsed in house.** A pure parser (five numeric fields, lists, ranges, steps; no names, no `@daily`), next fire from `zoneinfo`, no new dependency. A schedule needs `limits.budget_usd` and is at most hourly; `GRU_SCHEDULE_MIN_EVERY` can only raise that floor in this version.
8. **A generic features table.** `org_features(organization, feature, enabled, config, version)` and `domain/features.py` (`FeatureKey`, `FeatureSpec` with label, grant area, defaults and `validate`); feature data tables stay feature-specific; `GET /v1/organization/features` tells the sidebar what is on.
9. **Bundle storage.** One row per markdown file, frontmatter read through a port (`IOkfParser`, since the domain is standard-library only), the graph computed on read by a pure function; only the latest bundle per repository is kept; `viz.html` and non-markdown files are not stored. The UI renders markdown as text, never HTML: a bundle is untrusted text from a repository.
10. **GitOps: no. Permissions: a grant area.** The selection depends on GitHub installs and is operational state, and bundles are runtime data, so nothing is synced to `.gru` (a `solutions.yaml` can follow if customers ask). `GrantArea.SOLUTIONS` lets an owner or admin lend a member the right to switch it on, pick repositories and refresh, for a limited time; reading is for every member once the feature is on.
11. **Frozen surface, additive (beta).** New routes under `/v1/organization/features` and `/v1/solutions/*`, the `schedule:` field, `origin`, `GRU_SCHEDULE_INTERVAL` and `GRU_SCHEDULE_MIN_EVERY`, two metric series (52 pinned today), three audit actions (35), two plan limits (9), one grant area (8), migration `0025_solutions_foundation`. No MCP tool, no removal, no rename.

# Consequences

- A second backed-in feature is a `FeatureKey` and `FeatureSpec`, a grant area, a job or periodic on the scheduler and a stated GitOps decision; no new switch table and no private loop. CLAUDE.md records these rules.
- Every organization's spend by gru is explained in usage under a label, and a plain member can read every origin run. The cost is that origin runs cannot be hidden from members.
- A refresh that fails keeps the previous bundle readable; a failed repository carries a fixed `error_code`, never free text, and no log line carries a repository name, a path or a bundle body.
- Customer schedules are bounded by a mandatory budget, an hourly floor, one overlapping run at most (a tick is skipped while the last run is not finished, a run waiting at a gate included) and the plan's `max_schedules`.
- `max_solution_repos` and the three-at-a-time cap bound spend but the cap is per pass, not a lock; a follow-up can make it exact if replicas multiply.
- Repositories over the seeding limits (2000 files, 16 MiB, 1 MiB per file) are read leniently (oversized files, dependency folders and what does not fit are left out and listed; a repository is never refused for size; `too_large` stays only for a root tree GitHub cannot list); a code-search-based generator is a later step, as is a history of bundles, notifications and editing a schedule on the workflow board.
- The collector depends on the built-in OKF skills (the `gru` plugin of [decision 029](/decisions/029-claude-skills-panel.md)); the generation request and workflow are constants checked by tests.
