---
name: plumma-multi-command-partial-reply
description: Ask Plumma Connect for several signals in one call and handle the partial reply correctly — which blocks came back, which were skipped, which supplier answered, and exactly what you were charged for.
api: openapi/plumma-connect-openapi.yml
operations: [connectApi]
generated: '2026-09-07'
method: generated
source: openapi/plumma-connect-openapi.yml, errors/plumma-status-codes.yml, conventions/plumma-conventions.yml, plans/plumma-plans-pricing.yml
---

# Multi-command calls and partial replies

## Why batch
One request can carry several commands against the same number, and they are routed
independently. This is the main efficiency win of Plumma's single-endpoint design — and the
main place an integration goes wrong, because a batch almost never comes back uniformly.

```json
{ "number": "+447808226974",
  "commands": ["current_carrier", "line_classification", "sim_swap"] }
```

Each command must be unique in the array.

## The four things a response tells you

**1. The envelope status.** `status: 4` — "Partial reply" — is the normal outcome of a batch,
not a failure. It means at least one command was routed and at least one was not. `status: 0`
means all were routed; `status: 2` means none were.

**2. Which commands were skipped.** `status_message` carries a note of the form
`- Commands [age_verification] not available in [IT]`. Those commands were never sent to any
supplier, produce no response block, and **cost nothing**.

**3. Which supplier served what.** The bracketed suffix gives one entry per *routed* command:

```
Partial reply merged from multiple operators - Commands [scam_check] not available in [IT]
[cmd_enc=17] [current_carrier: MNO (A) : OK | kyc_match: MNO (B) : no data]
```

The third part of each entry is the supplier's own reason where it gave one (`no data`, a
timeout description) or a generic `OK` / `failed`.

**4. `cmd_enc`, for machines.** A single integer packing a 2-bit outcome per command ordinal
(`0` success, `1` client error, `2` provider error), bit position `ordinal * 2`. Ordinals:
0 line_classification, 1 current_carrier, 2 issuing_carrier, 3 porting_timestamp,
4 porting_logs, 5 network_presence, 6 roaming_intel, 7 deactivation_point, 8 churn_tracker,
9 sim_swap, 10 port_fraud_shield, 11 digital_footprint, 12 age_verification, 13 kyc_match,
14 divert_detector, 15 commercial_segment, 16 tenure_period, 17 qdr_history,
18 number_verification, 19 scam_check.

**Caveat that matters:** a command you never requested also reads as `0` in `cmd_enc`. The
field alone cannot separate "succeeded" from "never asked". Always cross-reference against the
commands you sent and the "not available" note.

## Read it command by command
Do not stop at the top-level status. Map each requested command to its response field — the
names do not always match (`sim_swap` → `simswap`, `commercial_segment` → `market_segment`,
`porting_timestamp` → `ported`/`ported_date`). The full binding is in
`data-model/plumma-data-model.yml`.

## What you pay for
Per command an operator actually **served**. In a partial reply you are charged for the served
ones only. A skipped command is free.

**One exception, and it is the expensive one.** Five commands are *global* —
`current_carrier`, `line_classification`, `issuing_carrier`, `digital_footprint`,
`roaming_intel`. They are served and billed for **every** country, so the no-coverage
exemption does not protect you. A burst of out-of-coverage traffic is free only if the batch
contains no global command.

An answer meaning "I hold no data for this" (a `-1` sentinel) is a served result and **is**
billed.

## Budgeting
Sandbox: 2 RPS, 180 **commands** per calendar day — a request carrying 18 commands consumes
18 of the budget, not 1. Production: 5 RPS, raisable on request. No rate-limit headers are
returned, so track your own count; you will find out you are over only from a `402` (demo) or
`429` (production). A zero wallet balance returns `402` and deactivates the demo key too.
