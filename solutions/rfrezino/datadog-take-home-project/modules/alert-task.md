---
type: "Module"
title: "High traffic alert task"
description: "Detects more than N loglines in one second within a window."
---
Takes lines within the last `window_in_minutes` up to now, groups them per epoch second, and the alert is on if any group exceeds
the threshold. A message is logged only when the on/off state changes: "High traffic generated an alert - hits = N, triggered at T" or "High traffic incident recovered at T".
