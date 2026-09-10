---
name: Build a local-business lead list
description: Turn a "what + where" query into a structured business list (name, phone, website, rating, review count) from Google Maps via CrustAPI.
api: CrustAPI
operations:
  - search
---

# Build a local-business lead list

Uses `GET /v1/search` (operationId `search`) with `type=maps`.

## Steps

1. Authenticate with the `x-api-key: key_live_...` header on every request.
2. Call the maps surface:
   ```
   GET https://crustapi.com/v1/search?type=maps&q=dentists&location=Miami, FL&limit=50
   ```
   - `q` — the business category / query.
   - `location` — city/state string (maps/places).
   - `limit` — businesses to return (default 20, max 100).
3. Read the `places[]` array from the `SearchResult` envelope. Each `Place` carries `title`, `address`, `latitude`/`longitude`, `rating`, `ratingCount`, `reviewsCount`, `category`, `phoneNumber`, `website`.
4. Map fields to your table/CSV. The CLI can do this directly: `crust search dentists --type maps --location "Miami, FL" --csv > leads.csv`.

## Rules

- Billing: Maps is billed per business returned; empty results are free.
- Rate limits: maps has no in-flight concurrency limit. On 429, honor `Retry-After`.
- Errors: `400` missing/invalid param, `401` bad key, `402` out of credits (top up or use x402), `429` rate limited. Bodies are `{"error": "..."}` (not RFC 9457).
