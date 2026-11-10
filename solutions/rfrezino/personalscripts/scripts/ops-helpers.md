---
type: "Script Group"
title: "Ops helper scripts"
description: "Jenkins worker tunnel, k9s, window management"
---

- `connect_to_jenkins_worker.sh`: gets GKE credentials for cluster `jenkins-worker` (project `freshbooks-builds`), opens an IAP SSH tunnel through `jenkins-helm` (local 9191 -> 8888), and notes `HTTPS_PROXY=localhost:9191`. Must be sourced for the export to persist.
- `k9s-builds.sh`: zsh variant: gcloud login, tunnel on port 8888, sets `HTTPS_PROXY`, launches `k9s` if installed.
- `window_management/move_to_space.sh <n>`: moves a window to a macOS space (yabai-style).
- `windows/quake-windows-bash.ahk`: AutoHotkey quake-style terminal shortcut.
