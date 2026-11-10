---
type: "Script"
title: "what-shard.py"
description: "Maps a numeric system id to its DB shard in production and staging"
---

Usage: `what-shard.py <systemid>`.

Loads `shards/production-values.yaml` and `shards/staging-values.yaml` (Helm-style values with an `env` list), reads the `DB_ENV_SHARDS` entry, a space-separated list of `name:maxId` ranges. Each shard starts at the previous end + 1 (first starts at 1). Prints the shard for both environments; exits 1 if the value is missing or the id is not an integer.

Part of the [overview](/overview.md).
