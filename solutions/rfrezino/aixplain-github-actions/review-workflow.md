---
type: "Process"
title: "PR review workflow"
description: "Step-by-step behaviour of AiAssistent.execute, including comment dedup via hidden markers."
---
# Steps

1. Skip PRs authored by dependabot.
2. Collect the latest version of each PR file from the commits.
3. Drop files by content match, then by path pattern.
4. Delete stale bot comments: summary comments, "All Good Here!" comments, unparsable ones, files removed from the PR, or whose blob SHA changed.
5. Skip files already commented with the same SHA (read from the hidden `<!--FILE:name|sha-->` lines in the summary comment).
6. Generate a comment per file with a prompt chosen by fnmatch (`*.py`, `*.md`, `*.*`, `*`). Replies containing "All Good Here!" are dropped.
7. Post comments plus a `### AIxplain Summary` comment.

Comments use header `### AIxplain Comment`, `#### File: _name_` and `<!--#### SHA: x -->` for parsing.
Implemented in [AiAssistent](/code/ai-assistent.md).

Known quirk: `get_all_bot_comments` has the condition `self.COMMENT_HEADER or ...`, which is always truthy, so every PR comment is treated as a bot comment candidate.
