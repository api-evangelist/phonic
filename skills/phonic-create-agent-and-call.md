---
name: Create a Phonic voice agent and place an outbound call
description: Configure a voice agent, attach a voice, and place an outbound phone call with it.
api: openapi/phonic-openapi-original.yaml
operations:
- agents.create
- agents.get
- voices.list
- conversations.outbound-call
- conversations.get
---

# Create a Phonic voice agent and place an outbound call

Base URL: `https://api.phonic.ai/v1`
Auth: `Authorization: Bearer <PHONIC_API_KEY>` (server-side only; never ship the API key to a browser).

## Steps

1. **Pick a voice.** `GET /voices` (`voices.list`) to browse available voices; grab an `id`
   (or `GET /voices/{id}` to inspect one).
2. **Create the agent.** `POST /agents` (`agents.create`) with a `name`, the chosen `voice`,
   and a system prompt. To create-or-update by name instead, use `PUT /agents/upsert` (`agents.upsert`).
3. **Verify.** `GET /agents/{nameOrId}` (`agents.get`) - agents are addressable by name or id.
4. **Place the call.** `POST /conversations/outbound_call` (`conversations.outbound-call`) with
   `to_phone_number` and a config referencing the agent and an optional `welcome_message`.
   Note the tighter rate limit here: 5 requests/second per organization.
5. **Track the conversation.** `GET /conversations/{id}` (`conversations.get`) to poll state, or
   subscribe to the `conversation.ended` webhook instead of polling.

## Rules

- Errors return `{ "error": { "message", "code" } }` (see errors/phonic-problem-types.yml).
- 429 = rate/concurrency limit reached; back off (outbound_call is 5 rps).
- No idempotency key is supported; do not blindly retry `outbound_call` on timeout - check
  conversation state first.
