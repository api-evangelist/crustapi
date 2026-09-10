---
name: Refresh a contact database in bulk with webhook delivery
description: Send up to 10,000 LinkedIn URLs in one request and receive results synchronously or via webhook, to keep a contact/candidate database current.
api: CrustAPI
operations:
  - linkedinBatch
---

# Refresh a contact database in bulk

Uses `POST /v1/linkedin/batch` (operationId `linkedinBatch`).

## Steps

1. Send `x-api-key: key_live_...`.
2. Small batch (<=100 URLs) — synchronous, results return inline in the order sent:
   ```
   POST https://crustapi.com/v1/linkedin/batch
   {"type": "company", "urls": ["...", "..."]}
   ```
   Types: `refresh`, `person` (a.k.a. `profile`), `company`, `posts`. Use `/in/` URLs for person/refresh/posts and `/company/` URLs for company. Each row carries either data or its own error, so one dead row never fails the rest.
3. Large batch (up to 10,000 URLs) — add a `webhook` URL to the body. The API returns `202` with a `jobId`. It runs in the background and POSTs the full results to your webhook when done; the `X-Crustapi-Job` header carries the jobId.
4. Poll progress (counts only): `GET https://crustapi.com/v1/linkedin/batch?id=<jobId>`. Results are delivered to the webhook and not retained.

## Rules

- Rows that come back not-accessible are free; transient blocks are retried automatically.
- Webhook delivery is retried once on failure — make your endpoint idempotent on `X-Crustapi-Job`.
- Errors: `400`/`401`/`402` as elsewhere; bodies are `{"error": "..."}`.
