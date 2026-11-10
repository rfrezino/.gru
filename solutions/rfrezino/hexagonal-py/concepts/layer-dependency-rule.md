---
type: "Concept"
title: "Layer dependency rule"
description: "Core rule hexagonal-py enforces between layers"
---
# Rule
Layers are ordered; index 1 is the innermost. A file may import from inner layers (lower index) but never from outer ones. Violations are `DependencyFlowError`s, checked by [the flow checker](/components/dependency-flow-checker.md).

Layers can hold several directory groups; files in one group importing from another group of the same layer is also an error (e.g. MySql vs Postgres). See [configuration](/concepts/configuration.md).
