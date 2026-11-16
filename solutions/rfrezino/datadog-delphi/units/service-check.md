---
type: Delphi Unit
title: TDataDogServiceCheck
description: Service-check object doubling as the connection config (host/port) for the sender.
resource: delphiDatadog.serviceCheck.pas
tags: [delphi]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-16T00:00:00Z" }
---
Defaults: Hostname `localhost`, Port 8125, Status dssUndefined. Setting an empty hostname or port 0 restores the defaults.

`ToStatsDString` builds `_sc|name|status[|d:ts][|h:host]|#tags[|m:message]`.

The [sender](/units/statsdclient-sender.md) reads Hostname and Port from an instance of this class.
