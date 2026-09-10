---
name: Look up a public LinkedIn profile or people search
description: Retrieve a public LinkedIn profile, company, posts, or run a people search as structured JSON via CrustAPI.
api: CrustAPI
operations:
  - linkedin
---

# Look up a public LinkedIn profile or people search

Uses `GET /v1/linkedin` (operationId `linkedin`). Pick the surface with `type`.

## Steps

1. Send `x-api-key: key_live_...`.
2. Profile by URL:
   ```
   GET https://crustapi.com/v1/linkedin?type=profile&url=https://www.linkedin.com/in/williamhgates
   ```
   Returns name, headline, location, about, work history, education, skills.
3. Company by URL: `type=company&url=https://www.linkedin.com/company/...`.
4. People search: `type=search` (alias `people`) with `keywords`, `title`, `location`, `company`, `school`, `industry`, and follower/connection/experience ranges; add `enrich=true` to return each person's full profile in the same call.
5. Paginate with `start`/`limit` (people default 10, max 50).

## Rules

- Billing depends on the surface (see plans/crustapi-plans-pricing.yml). Profiles that are not accessible / private are free.
- LinkedIn surfaces have no in-flight concurrency limit; on `429` honor `Retry-After`.
- Public data only — the kind visible without logging in.
