---
name: Send a cross-border payout with AZA Finance
description: Create a sender, quote and create a transaction, fund it, and track it to a paid state on the TransferZero API V1.
api: openapi/aza-finance-openapi-original.json
operations: [calculate-transactions, post-senders, post-transactions, create-and-fund-transaction, payin-transaction, get-transaction]
---

# Send a cross-border payout

Use the TransferZero (AZA Finance) API V1 to move funds to a recipient in Africa.

## Auth
Sign every request with HMAC: `Authorization-Key`, `Authorization-Nonce`, `Authorization-Signature`
(or use `Authorization-Key` + `Authorization-Secret`). Sandbox base URL
`https://api-sandbox.transferzero.com/v1`; production `https://api.transferzero.com/v1`.

## Steps
1. (Optional) Quote the FX and fees with `calculate-transactions` (`POST /transactions/calculate`)
   using the input/output currencies and amount.
2. Create the sender with `post-senders` (`POST /senders`). Set a stable `external_id`; if a
   sender with that `external_id` already exists the API returns the existing one under
   `meta.existing` (safe to retry).
3. Create the transaction with `post-transactions` (`POST /transactions`), attaching the
   sender and one or more recipients (each with a payout method). Again pass an `external_id`
   for idempotent creation.
4. Fund it: either use `create-and-fund-transaction` (`POST /transactions/create_and_fund`)
   to create and fund in one call, or fund an existing transaction with `payin-transaction`
   (`POST /transactions/{Transaction ID}/payin`).
5. Poll `get-transaction` (`GET /transactions/{Transaction ID}`) until the recipient state
   settles. In sandbox, drive the outcome with the recipient account/phone ending
   (`00` = paid, `01` = pending, `39` = recipient_error — see sandbox/aza-finance-sandbox.yml).

## Rules
- Idempotency is `external_id`-based; reuse the same value on retries — do not generate a new one.
- Errors return a field-keyed `errors` map (see errors/aza-finance-problem-types.yml), not RFC 9457.
- Subscribe to webhooks (skills: aza-finance-webhooks) instead of tight polling in production.
