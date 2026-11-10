---
type: "Component"
title: "Configuration loading"
description: "HexagonalConfig, HexagonalLayer, HexagonalComposition and TomlConfiguration"
---
# Pieces
- `hexagonal_config.py`: `HexagonalConfig` singleton `hexagonal_config` with `excluded_dirs` and an ordered `HexagonalComposition` (list subclass, outermost first).
- `domain/hexagonal_layer.py`: `HexagonalLayer` dataclass (name, directories_groups) with path validation.
- `services/toml_configuration.py`: parses pyproject; layer keys sorted descending so outermost comes first.
- `hexagonal_project_config.py`: example config.

See [configuration](/concepts/configuration.md).
