---
type: Schema
title: Guru GraphQL model
description: The Guru type in the Amplify AppSync schema.
tags: [guru]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-13T00:00:00Z" }
---

# Schema

Defined in `guru_managment/amplify/backend/api/gurus/schema.graphql` as `type Guru @model`.

| Field | Type |
|---|---|
| id | ID! |
| name, gender, background, teachings, distinctPersonalityTraits, physicalAndClothing, color, language | String! |
| influencedBy | [String!] |

Auth: `AMPLIFY { globalAuthRule: AuthRule = { allow: public } }`, for testing only. Consumed by [guru_managment](/components/guru-managment.md) and the [Lambda](/components/job-daily-messages-lambda.md); [guru_app](/components/guru-app.md) uses a local copy of the same shape.
