---
tags: [aixplain]
type: Module
title: GithubPR wrapper
description: PyGithub wrapper for PR files, commits, comments and contents.
resource: src/github_pr.py
generated: { by: claude-code/sonnet-5.5, at: "2026-11-16T00:00:00Z" }
---
Wraps `Github(token).get_repo`. Methods: `get_comments`, `remove_old_comments`, `get_pr_commits`, `get_files`, `add_comments` (issue comments), `get_content_for_file` (at a commit sha), `get_pr_title`, `get_pr_author_login`. Used by [AiAssistent](/components/ai-assistent.md).
