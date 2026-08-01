---
name: List, analyze, and extract data from Phonic conversations
description: Page through conversations, pull analysis, and run structured data extraction against them.
api: openapi/phonic-openapi-original.yaml
operations:
- conversations.list
- conversations.get
- conversations.get-analysis
- extractionSchemas.create
- conversations.extract-data
- conversations.list-extractions
---

# List, analyze, and extract data from Phonic conversations

Base URL: `https://api.phonic.ai/v1`
Auth: `Authorization: Bearer <PHONIC_API_KEY>`.

## Steps

1. **List conversations.** `GET /conversations` (`conversations.list`). Page with cursor params:
   `after` (forward) / `before` (backward) - mutually exclusive - and `limit` (default 20).
   Read `pagination.next_cursor` / `pagination.prev_cursor` from the response to continue.
   Filter by `started_at_min` / `started_at_max`.
2. **Inspect one.** `GET /conversations/{id}` (`conversations.get`) and
   `GET /conversations/{id}/analysis` (`conversations.get-analysis`).
3. **Define an extraction schema.** `POST /extraction_schemas` (`extractionSchemas.create`) to
   declare the structured fields you want pulled from calls.
4. **Extract.** `POST /conversations/{id}/extractions` (`conversations.extract-data`) referencing
   the schema, then `GET /conversations/{id}/extractions` (`conversations.list-extractions`) to read results.

## Rules

- Pagination cursors are resource IDs, not opaque page tokens; `before` and `after` cannot be combined.
- Deleting/replaying an active conversation returns 409; a conversation with no audio/agent returns 422 on replay.
- Errors return `{ "error": { "message", "code" } }` (errors/phonic-problem-types.yml).
