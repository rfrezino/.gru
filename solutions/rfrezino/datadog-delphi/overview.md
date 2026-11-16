---
type: Project
title: datadog-delphi overview
description: Delphi client that formats DogStatsD datagrams and sends them over UDP.
tags: [delphi, datadog, statsd]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-16T00:00:00Z" }
---
# datadog-delphi

A Delphi library for sending metrics, events and service checks to a Datadog agent using the StatsD wire format. Provided by Coolblue (MIT-style license file in repo). The README says "Java applications", which is a leftover from the Java original this ports.

Architecture: a client ([stats client](/units/statsdclient-impl.md)) formats text lines and hands them to a [sender](/units/statsdclient-sender.md), which queues them and flushes over UDP from a background thread. Shared types are in [header](/units/header.md); helpers in [utils](/units/utils.md). See [usage](/guides/usage.md).
