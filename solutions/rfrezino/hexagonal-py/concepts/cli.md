---
type: Interface
title: Command line interface
description: click CLI with check and diagram commands
tags: [hexagonal-py]
generated:
  by: claude-code/sonnet-5.5
  at: "2026-11-16T00:00:00Z"
---

Defined in `src/hexagonal/infrastructure/cli.py`; script `hexagonal = hexagonal.infrastructure.cli:main`.

| Command | Options | Behaviour |
|---|---|---|
| `check` | `--project_path` (req), `--source_path` (req), `--hexagonal_config_file` (default `hexagonal_config.py`) | Runs the [check use case](/concepts/check-use-case.md); prints numbered errors and exits 1 if any. |
| `diagram` | same, plus `--show` (default true) | Runs the [diagram use case](/concepts/diagram-use-case.md). Requires Graphviz. |

Configuration is loaded first from `pyproject.toml` in project path, otherwise from the config file in the source path; see [Configuration](/concepts/configuration.md). Source path is appended to `sys.path`.

```
hexagonal check --project_path ./ --source_path ./src
```
