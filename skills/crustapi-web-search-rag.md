---
name: Pull web search + page content for RAG
description: Run a Google web search and fetch clean page text/markdown to ground a retrieval-augmented generation pipeline via CrustAPI.
api: CrustAPI
operations:
  - search
---

# Pull web search + page content for RAG

Uses `GET /v1/search` (operationId `search`) across the `web` and `webpage` surfaces.

## Steps

1. Send `x-api-key: key_live_...`.
2. Web search:
   ```
   GET https://crustapi.com/v1/search?type=web&q=best coffee in austin&gl=us&hl=en
   ```
   Read `organic[]` (`title`, `link`, `snippet`, `position`) from the `SearchResult` envelope.
3. Fetch a page as clean text/markdown for chunking:
   ```
   GET https://crustapi.com/v1/search?type=webpage&url=https://example.com/article&includeMarkdown=true
   ```
   Read the `text` / `markdown` fields.
4. Paginate web results with `page`/`num`. Use `fresh=1` to bypass the up-to-60-second read cache.

## Rules

- Billing: web search is billed per successful request; empty results are free.
- Concurrency: web/news allow 2 (free) / 5 (paid) in-flight per key; on `429` honor `Retry-After`.
- The MCP `search` and `scrape_webpage` tools wrap these same calls (see mcp/crustapi-tool-crosswalk.yml).
