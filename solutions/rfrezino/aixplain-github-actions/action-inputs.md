---
tags: [aixplain]
type: Configuration
title: Action inputs
description: Inputs declared in action.yml and how they reach main.py.
resource: action.yml
generated: { by: claude-code/sonnet-5.5, at: "2026-11-16T00:00:00Z" }
---
# Inputs

| Input | Required | Default | Purpose |
|---|---|---|---|
| openai_api_key | no | | OpenAI key; selects ChatGPT |
| github_token | yes | | GitHub API token |
| github_pr_id | yes | | PR number |
| google_gemini_token | no | | Gemini token |
| ignore_files_with_content | no | | skip files containing this text |
| ignore_files_in_paths | no | | ";"-separated glob paths, e.g. `*/test/;*/docs/` |
| instructions | no | | ";"-separated extra review instructions |
| google_ai_model | no | gemini-2.0-flash-001 | Gemini model |
| google_project_name | no | | Google Cloud project (Vertex AI) |

Inputs are passed positionally to `entrypoint.sh`, which maps them to `--flags` of `src/main.py`. `GITHUB_REPOSITORY` comes from the environment.
