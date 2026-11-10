---
type: "Component"
title: "Parser Worker"
description: "Background scanning and parsing of pas/dcu files."
---
# Parser Worker

`TParserWorker` (`Source/FindUnit.Worker.pas`) lists `.pas` and `.dcu` files in directories, drops dcu files that already have a pas, generates pas from the rest via the [DCU Decompiler](/components/dcu-decompiler.md), parses files in parallel (`ParseFilesParallel`) and returns a `TUnits` ([Units Cache](/components/units-cache.md)) either synchronously (`Start`) or through a `TOnFinished` callback. Parsing is done by the [Pas Parser](/components/pas-parser.md).
