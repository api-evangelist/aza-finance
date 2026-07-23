---
name: Subscribe to and verify AZA Finance webhooks
description: Register a webhook endpoint, discover event types, verify the HMAC signature, and inspect delivery logs on the TransferZero API V1.
api: openapi/aza-finance-openapi-original.json
operations: [get-webhook-events, post-webhooks, get-webhooks, get-webhook-logs, get-webhook-log, delete-webhook]
---

# Subscribe to and verify webhooks

## Auth
HMAC-signed requests (`Authorization-Key` / `Authorization-Nonce` / `Authorization-Signature`).

## Steps
1. List available event types with `get-webhook-events` (`GET /webhooks/events`).
2. Register your callback URL and the events you want with `post-webhooks` (`POST /webhooks`).
3. Confirm registration with `get-webhooks` (`GET /webhooks`).
4. On each incoming callback, verify the HMAC signature over the raw body BEFORE trusting it
   (the SDKs expose `validate_webhook_request()`). Payloads arrive as transaction, sender,
   recipient, document or payout_method objects (see asyncapi/aza-finance-webhooks.yml).
5. Debug deliveries with `get-webhook-logs` (`GET /logs/webhooks`) and
   `get-webhook-log` (`GET /logs/{Webhook Log ID}`).
6. Remove a subscription with `delete-webhook` (`DELETE /webhooks/{Webhook ID}`).

## Rules
- Always verify the signature; never act on an unverified callback body.
- Respond 2xx quickly and process asynchronously; use the logs endpoints to replay/debug.
