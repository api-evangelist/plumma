---
name: plumma-sim-swap-fraud-check
description: Run a Plumma Connect SIM-swap check on a phone number and read the three-state answer correctly (swap found / watched and none found / operator holds nothing), without mistaking an HTTP 200 for a successful check.
api: openapi/plumma-connect-openapi.yml
operations: [connectApi]
generated: '2026-09-07'
method: generated
source: openapi/plumma-connect-openapi.yml, conventions/plumma-conventions.yml, errors/plumma-status-codes.yml, sandbox/plumma-sandbox.yml
---

# SIM-swap check with Plumma Connect

## When to use this
Before a high-value transaction, a password reset, or an SMS-OTP step, to find out whether
the SIM behind a phone number was changed recently. A recent swap is a strong account-takeover
signal.

## The one call

`POST https://connect.plumma.it/services/api` — operationId `connectApi`. This API has
exactly one operation; the *command* is what varies.

Headers:
- `x-plumma-connect-api-key: <base64 X.509 client certificate>` — required. Plumma's prose
  docs name this header `x-ploommacore-api-key`; the OpenAPI names it
  `x-plumma-connect-api-key`. If one is rejected, try the other and record which the
  deployment accepts.
- `x-plumma-connect-app-id: <numeric application id>` — **send it to run live. Omit it and
  the call silently becomes a sandbox call answered from canned data.** It will not fail.
- Never send `X-Correlation-ID`. The server generates it and returns it on every response.

Body:

```json
{ "number": "+393273339145", "commands": ["sim_swap"] }
```

`number` is E.164, with or without the leading `+`. Spaces, dashes, dots and parentheses are
rejected — pattern `^\+?[1-9][0-9]{6,14}$`.

## Reading the answer — do this before anything else

**HTTP 200 does not mean the check ran.** Read `status` in the body:

| `status` | Meaning | Billed | What to do |
|---|---|---|---|
| 0 | Every requested command was served | yes | proceed to the block |
| 1 | Invalid number — rejected at validation | no | fix the format; do not retry unchanged |
| 2 | No commands available for this number's country | no | do not retry; the answer will not change |
| 4 | Partial reply — some commands served, others not | yes, served only | read command by command |
| 5 | Unknown error | no | safe to retry; if it persists, quote `X-Correlation-ID` to support |

Then read `simswap`. It has three states and conflating them is the mistake this skill exists
to prevent:

- `risk_indicator` **> 0** with a `date` — a swap was found. `simswap_min_threshold` /
  `simswap_max_threshold` bound the window it was measured over.
- `risk_indicator` **0** — the operator watched and found no swap. `swapped: false` and
  `swapped_max_age: 240` carry the bounded claim: nothing in the last 240 hours. This is a
  real negative.
- `risk_indicator` **-1** — the operator holds nothing for this identifier at all. Upstream
  answered `422 SERVICE_NOT_APPLICABLE`: a landline, an M2M or data-only SIM, or an MVNO that
  has not enabled the API. **This is an answer, so it is served and billed** — but it is not
  a negative. Do not treat it as "no swap".
- The `simswap` block **absent entirely** — the command was never routed, or the supplier
  call got no answer. Check the `Commands [...] not available in [CC]` note in
  `status_message`.

## Cost and retries
You are billed per command an operator actually served, never per API call, from a prepaid
Euro wallet. **There is no idempotency key on this API.** A retry of a call that was already
served is a second charge. Retry only on `status: 5` or a `500`, and never in a loop.

## Rehearse first
Omit `x-plumma-connect-app-id` (or authenticate with the demo key) and the identical request
runs free against canned data with the identical response shape. The response carries
`This response is for demo purpose only` inside `status_message` — that exact substring is
the documented test for "not charged". Demo numbers published by Plumma include
`+393273339145` (IT), `+447808226974` (GB) and `+5581986179310` (BR); the authoritative list
is in the console sandbox. Sandbox is capped at 2 RPS and 180 commands/day; exceeding it
returns `402` with `{"code": "rate.limit.exceeded"}`.

## Errors at the transport layer
4xx/5xx return `application/problem+json` with `correlation_id` in the body. `401` means the
certificate itself is unusable — mint a new key. `403` means the certificate is fine but the
call is not allowed (header absent, wrong purpose, or the application is not admitted) — a
new key changes nothing; contact the account manager.

## Do not
- Do not use the result as the sole basis for a decision. Plumma's Terms (Art. 5.1) state
  responses are "informational and probabilistic in nature".
- Do not send real personal data to the sandbox — ToS Art. 2.2 forbids it.
