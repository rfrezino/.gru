---
type: Infrastructure
title: Amplify backend resources
description: AWS resources declared in amplify/backend/backend-config.json.
tags: [guru]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-13T00:00:00Z" }
---

# Resources

| Category | Name | Service |
|---|---|---|
| api | gurus | AppSync GraphQL, API_KEY default auth (key expires 2024-09-10) |
| api | dailyMessages | API Gateway |
| api | jobDailyMessages | API Gateway, fronts the Lambda |
| api | messageContext | API Gateway |
| auth | gurus5b7c2af2 | Cognito, email sign-up/username, min password 8, no MFA |
| function | jobDailyMessagesLambda | Lambda (Python), see [Lambda](/components/job-daily-messages-lambda.md) |
| function | guruslayerJobDailyMessages | Lambda layer (Python deps) |
| storage | dailyMessages | DynamoDB |
| storage | guruImage | S3 |

The GraphQL schema is in [Guru model](/data/guru-model.md); it uses a public global auth rule marked "FOR TESTING ONLY". The Lambda depends on the gurus API (endpoint, key), dailyMessages API/table and the guruImage bucket. Used by [guru_managment](/components/guru-managment.md).
