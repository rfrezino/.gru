---
type: "Component"
title: "CLI"
description: "Click CLI entry point for check and diagram"
---
# Commands
Entry `hexagonal.infrastructure.cli:main` (poetry script `hexagonal`).
- `hexagonal check --project_path P --source_path S [--hexagonal_config_file]`: runs [the check use case](/components/check-use-case.md); prints errors and exits 1 if any.
- `hexagonal diagram --project_path P --source_path S [--show]`: runs [the diagram use case](/components/diagram-use-case.md); needs Graphviz.

Config is loaded per [configuration](/concepts/configuration.md): pyproject first, else the config file.
