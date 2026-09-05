---
name: Pay for PontoFato calls with x402 or prepaid credit
description: >-
  Handle the HTTP 402 challenge on paid routes — pay per request with x402 (USDC on Base) or
  buy a prepaid cred_ token that debits across every endpoint.
api: openapi/pontofato-openapi-original.json
operations: [vizinhanca, contact, post_api_credito, get_api_credito]
generated: '2026-09-05'
method: generated
---

# Pay with x402 or prepaid credit

There is no signup and no API key. When a paid route needs payment it answers **HTTP 402**
with an x402 v1 body: `{"x402Version": 1, "accepts": [...]}` — scheme `exact`, network
`base` (chain 8453), asset USDC. Two doors:

## Door 1 — pay per request (x402)

1. Call the paid operation (`vizinhanca` past its 10/day free quota costs $0.05; the agent
   path of `contact` costs $0.10).
2. On 402, read `accepts[0]` (`payTo`, `maxAmountRequired`, `asset`), settle the USDC
   payment, and **repeat the identical request with the `X-PAYMENT` header**.
3. There is no documented replay protection on a resubmitted payment — do not double-submit,
   and treat a paid call as non-idempotent.

## Door 2 — prepaid credit

1. `post_api_credito` — `POST /api/credito?usd=<1|5|10|25>` (other amounts get 400). The
   first call 402s; pay it once via x402 and the response returns a bearer **`cred_` token**.
2. Spend it anywhere in the house: send `Authorization: Bearer cred_…` (or the `X-Credito`
   header) on paid calls; the balance debits per call.
3. `get_api_credito` — `GET /api/credito` with the token returns balance and recent
   statement (it never echoes the token back).

The token is a bearer of balance, not an account — losing it loses the balance, so store it
like a secret. No refund or reversal operation is documented for a credit purchase.
