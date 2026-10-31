# Plumma

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Plumma SRL is an Italian software house (VAT IT04209450362, REA MO-451932) with its registered office at Via Corsini 21, Fellicarolo, Fanano (Modena) and an operational office at Via Provinciale 175/B, Crespellano, Valsamoggia (Bologna). Its own product, Plumma Connect, is an aggregation gateway in front of mobile network operators: it sells enterprises a single integration point for the operator-held identity, SIM and network-state signals that the GSMA Open Gateway programme exposes, instead of requiring the buyer to onboard operator-by-operator and country-by-country. Plumma states it is a GSMA Open Gateway channel partner and names GSMA, TIM, AWS and WaveMaker among its partners.

Its architectural position is the notable part, and it is deliberate. Rather than mirroring the CAMARA one-endpoint-per-capability shape, Plumma Connect publishes a SINGLE OpenAPI 3.1 operation — POST /api on https://connect.plumma.it/services — that takes a phone number in E.164 form plus a list of named commands (sim_swap, kyc_match, age_verification, current_carrier, porting_logs, roaming_intel, churn_tracker, digital_footprint and others), so the CAMARA surface is an implementation detail behind an abstraction layer rather than the developer contract. The company is not, however, standard-averse underneath: its KYC request schema carries the CAMARA KnowYourCustomer Match attribute vocabulary verbatim (in snake_case), and every 4xx/5xx returns an RFC 7807 problem document with a correlation_id. The divergence is at the endpoint shape, not the data model.

The product is positioned for fraud prevention and identity verification, with company-stated coverage of 17 live countries across Europe, the Americas, APAC and Africa and 75+ network connections. Documentation, the OpenAPI and a draft-07 request schema are public and ungated, and the JavaScript documentation portal is backed by an unauthenticated JSON content API at connect.ploomma.com that serves 72 documents including the billing guide, SLA, terms and full command reference. Billing is EUR 200/month plus a prepaid wallet charged per served command; published rate limits are 2/5 RPS and 180 commands/day. There is no MCP server, no agent card, no SDKs and no /.well-known documents — the provider marks SDKs and webhooks 'under construction'.

**Areas:** Telecommunications · Network APIs · Italy · Europe · GSMA Open Gateway · CAMARA · API Aggregator · Fraud Prevention · Identity Verification · SIM Swap · KYC · Age Verification

## Plumma on APIs.io

Everything profiled for Plumma is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/plumma/](https://apis.io/providers/plumma/)** — or go straight to one of the APIs or properties below.

### APIs (1)

- [Plumma CONNECT API](https://apis.io/apis/plumma/plumma-connect-api/) — The flagship aggregation endpoint.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [plumma-connect](https://apis.io/servers/plumma/plumma-connect/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Plumma Agentic Access](https://apis.io/agentic-access/plumma/plumma-agentic-access/)

#### JSON Schema (1)

The data models the API reads and writes.

- [PlmRequest](https://apis.io/schemas/plumma/plumma-plmrequest.schema/)

#### Examples (1)

Example requests and responses.

- [Plumma Connect Api Examples](https://apis.io/examples/plumma/plumma-connect-api-examples/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Plumma Connect Api Overlay](https://apis.io/overlays/plumma/plumma-connect-api-overlay/)

#### Security (4)

Authentication, trust centre, and vulnerability disclosure.

- [Plumma Authentication](https://apis.io/security/plumma/plumma-authentication/)
- [Plumma Domain Security](https://apis.io/security/plumma/plumma-domain-security/)
- [Plumma Trust Center](https://apis.io/security/plumma/plumma-trust-center/)
- [Plumma Vulnerability Disclosure](https://apis.io/security/plumma/plumma-vulnerability-disclosure/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Plumma Plans Pricing](https://apis.io/plans/plumma/plumma-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Plumma Rate Limits](https://apis.io/rate-limits/plumma/plumma-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.plumma.it)
- [Documentation](https://connect.plumma.it/plumma-connect-docs/)
- [API reference](https://connect.plumma.it/plumma-connect-docs/)
- [Sign up](https://connect.ploomma.com/)
- [Support](https://connect.plumma.it/support/)
- [Blog](https://connect.plumma.it/blog/)
- [Compliance](https://connect.plumma.it/plumma-connect-docs/#security)
- [Terms of service](https://connect.plumma.it/plumma-connect-docs/#terms-and-conditions)
- [Privacy policy](https://www.plumma.it/privacy-policy.html)
- [Status](https://oneuptime.com/status-page/5c4b010b-1753-4d8f-8689-226d6e86d72a)

## API Evangelist rating

**Kin Score 53.4** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 66.1 |
| Contract quality | 62.1 |
| Contract governance | 22.0 |
| Operational transparency | 47.4 |
| Developer ergonomics | 54.2 |
| Access clarity | 50.0 |

**Agent readiness 34.4** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | verified |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | yes |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | na |

**Regulatory alignment 35.8** — Telecommunications

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/plumma/](https://apis.io/providers/plumma/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Plumma: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/plumma/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
