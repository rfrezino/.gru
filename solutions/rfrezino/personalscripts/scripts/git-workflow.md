---
type: "Script Group"
title: "Git workflow scripts"
description: "gitSyncAndClean, git-sync-and-clean, git-finish-feature, git-pr, opengithub, delete.sh"
---

| File | Purpose |
|---|---|
| `gitSyncAndClean.py` | Library using GitPython (`local_repo`, `gitSyncAndClean`, `get_master_branch_name`): syncs the main branch with `origin`/fork remote `fb` and cleans merged branches. |
| `git-sync-and-clean` | Entry point calling `gitSyncAndClean()`. |
| `git-finish-feature` | Aborts if `git status` shows modified files; else checks out the main branch and runs sync-and-clean. |
| `git-pr` | On a `feature` branch: pushes to origin, derives `TEAM-ID` from branch `feature/TEAM-123-...`, and runs `gh pr create` with title `[TEAM-ID] <arg>` and a Jira link body. |
| `opengithub.py`, `cbopengithub.py` | Read `.git/config`, find the `fbrodrigorezino` remote and open the repo in a browser (freshbooks org). |
| `delete.sh` | Fetches/prunes and deletes remote branches (except main/master) older than a cutoff (uses macOS `date -v`). Destructive. |
