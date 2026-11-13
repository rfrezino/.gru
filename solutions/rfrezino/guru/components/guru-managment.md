---
type: Application
title: guru_managment (admin Flutter app + Amplify backend)
description: Admin Flutter web app and AWS Amplify backend for gurus and daily messages.
tags: [guru]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-13T00:00:00Z" }
---

# Overview

`guru_managment/` is a Flutter admin app plus the AWS Amplify project under `amplify/`.

- `lib/main.dart`: `HomeApp` configures Amplify (API, Cognito auth, S3 storage), wraps the UI in the Authenticator, and shows tabs "Gurus", "Message Types", "Api Tests".
- `lib/repositories/guru_repository.dart`: `GuruRepository` does create/update/delete of [Guru](/data/guru-model.md) via AppSync `ModelMutations` and uploads guru images to S3 (key = guru name).
- `lib/gurus/`: list, form route and form widget; `lib/widgets/`: text field, text area, gender/language dropdowns, image picker, colour picker, load-image-from-disk.
- `lib/models/Guru.dart`, `ModelProvider.dart`: generated Amplify models.

# Backend

See [Amplify backend](/components/amplify-backend.md) and the [daily message Lambda](/components/job-daily-messages-lambda.md).
