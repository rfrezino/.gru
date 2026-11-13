---
type: Application
title: guru_app (consumer Flutter app)
description: Flutter app that lists gurus from a hardcoded list and shows details.
tags: [guru]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-13T00:00:00Z" }
---

# Overview

`guru_app/` is the end-user Flutter app (Android, iOS, web). `lib/main.dart` runs `GuruListView` from `lib/gurus/gurus_widget.dart`; `lib/gurus/guru_details_view.dart` shows one guru.

Guru data is not fetched from the backend: `GuruList.getList()` in `lib/gurus/gurus.dart` returns seven hardcoded `Guru` objects (e.g. Guru Anila Radhika, Guru Saya Lin). Photos come from `assets/<Name_with_underscores>.png`.

Fields mirror the [Guru model](/data/guru-model.md), with `predominantColor` as a Flutter `Color`. The managed data lives in the [management app](/components/guru-managment.md).
