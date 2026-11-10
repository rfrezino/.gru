---
type: "Component"
title: "TDataDogServiceCheck"
description: "Agent endpoint configuration (host/port) plus service-check StatsD line rendering."
---
Defaults: host `localhost`, port 8125 (empty host or port 0 revert to defaults), status `dssUndefined`. `ToStatsDString` renders `_sc|name|status[|d:ts][|h:host]|#tags[|m:msg]`. In practice it is passed to the [sender](/components/sender.md) as the connection config.
