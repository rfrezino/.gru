---
type: "API"
title: "REST API"
description: "HTTP endpoints exposed by the backend."
---
Routers subclass `Routable` (`src/host/restapi/resources/`) and use `@get` style decorators.

| Method | Path | Response |
|---|---|---|
| GET | /health | `{"value": "Hello World"}` |

No day-context endpoints exist yet.
