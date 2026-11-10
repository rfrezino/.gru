---
type: "Interface"
title: "Action inputs and entrypoint"
description: "Inputs declared in action.yml and how they reach src/main.py."
---
# Inputs

| Input | Required | Default | Meaning |
|---|---|---|---|
| openai_api_key | no | | Selects ChatGPT when non-empty |
| github_token | yes | | GitHub API token |
| github_pr_id | yes | | PR number |
| google_gemini_token | no | | Used when no OpenAI key |
| ignore_files_with_content | no | | `;`-separated strings; files containing them are skipped |
| ignore_files_in_paths | no | | `;`-separated fnmatch patterns, e.g. `*/test/;*/docs/` |
| instructions | no | | `;`-separated extra review instructions |
| google_ai_model | no | gemini-2.0-flash-001 | Gemini model |
| google_project_name | no | | Google project |

`entrypoint.sh` passes the args positionally to `python /src/main.py` as flags; the
[Dockerfile](/overview.md) is python:3.11-slim. `main.py` calls [execute](/code/execute.md).
