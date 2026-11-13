---
type: Service
title: jobDailyMessagesLambda
description: Python Lambda that generates one AI-written daily message per date and stores it in DynamoDB.
tags: [guru]
generated: { by: "agent/claude-sonnet-5-5", at: "2026-11-13T00:00:00Z" }
---

# Flow

Source: `guru_managment/amplify/backend/function/jobDailyMessagesLambda/src/`.

1. `index.handler(event, context)` takes optional `day`, `month`, `year` (default: today).
2. `check_if_message_alredy_exists` (`messageBuilders/daily_messages_repository.py`) looks up key `d/m/yyyy` in the DynamoDB table named by `STORAGE_DAILYMESSAGES_NAME`; if present it returns 200 "already exists".
3. `generate_context.get_context` builds a context: date metadata (weekday, month name, start/end of month, closeness to year end), a random uplifting message type (e.g. Gratitude, Compassion, Courage), a random mystical perspective type, number of gurus chatting, and gurus fetched from the [GraphQL API](/data/guru-model.md) using `API_GURUS_GRAPHQLAPIENDPOINTOUTPUT` and `API_GURUS_GRAPHQLAPIKEYOUTPUT`. About 10% of messages are in parables.
4. `messageBuilders/openai.py` builds a system/user prompt (single guru vs. several gurus talking), asks OpenAI for JSON with `message`, `gurus`, `shortMessage`, `imageDescription`; the API key is read from SSM. It also handles image generation (uses PIL, boto3).
5. The result `{version: 1, content, date, context}` is saved with `save_message_to_db` and returned with CORS headers.

Tests live in `tests/` (`test_generate_context.py`, `test_daily_messages_repository.py`). Part of the [Amplify backend](/components/amplify-backend.md).
