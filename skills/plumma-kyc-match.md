---
name: plumma-kyc-match
description: Score caller-supplied identity attributes against mobile-operator records with Plumma Connect KYC Match, choosing the right address form and reading per-field scores where -1 does not mean zero.
api: openapi/plumma-connect-openapi.yml
operations: [connectApi]
generated: '2026-09-07'
method: generated
source: openapi/plumma-connect-openapi.yml, data-model/plumma-data-model.yml, errors/plumma-problem-types.yml
---

# KYC Match with Plumma Connect

## What it does
You send identity attributes you already hold about a person — name, date of birth, address,
national id, email — alongside their phone number. The operator compares them against its own
subscriber record and returns a **match score per field**. Your data is scored, never
returned, and the operator's data never leaves the network. Nothing is persisted at rest.

## The call

`POST https://connect.plumma.it/services/api` — operationId `connectApi`, command `kyc_match`.

Two address forms, and they are **mutually exclusive**. Supplying neither is a `400` whose
`detail` reads `Plumma kyc_challenges must have either address or inline_address populated`.

Structured address:

```json
{
  "number": "+393273339145",
  "commands": ["kyc_match"],
  "kyc_challenges": {
    "name": { "first_name": "Luigi", "last_name": "Armani" },
    "dob":  { "day": 28, "month": 2, "year": 1999 },
    "address": {
      "street": "Via Corsini", "street_no": "21",
      "city": "Fanano", "province": "MO",
      "postcode": "41021", "country": "IT"
    }
  }
}
```

Free-text address, with normalisation:

```json
{
  "number": "+447808226974",
  "commands": ["kyc_match"],
  "kyc_challenges": {
    "name": { "first_name": "John", "last_name": "Smith" },
    "inline_address": { "address": "23 Omnia Street, London, EC1A 1BB, GB", "normalize": true }
  }
}
```

`Address` requires `street`, `street_no`, `city`, `postcode` and `country`. `InlineAddress`
requires only `address`. Setting `normalize: true` on either also returns a cleansed
`normalized_address` block.

## If you already speak CAMARA
`kyc_challenges` is the CAMARA KnowYourCustomer *Match* attribute set, snake_cased one for
one. `nameKanaHankaku` → `name_kana_hankaku`, `nameKanaZenkaku` → `name_kana_zenkaku`,
`familyNameAtBirth` → `family_name_at_birth`, `houseNumberExtension` →
`house_number_extension`, `streetNumber` → `street_no`, `idDocument` → `national_id`. Map
mechanically; only the envelope differs.

## Reading `kyc_results`
One `*_score` per attribute you supplied: `first_name_score`, `last_name_score`,
`middle_name_score`, `name_score`, `address_score`, `street_score`, `street_no_score`,
`city_score`, `province_score`, `postcode_score`, `country_score`, `dob_score`,
`national_id_score`, `email_score`.

- **0–100** — a real match score for that field.
- **`0`** — the operator compared and did not match. A genuine negative.
- **`-1`** — the operator **holds no data for that field**. Not a mismatch. Treat it as
  unknown, and do not fold it into an average as a zero. It is still a served, billed answer.
- **field absent** — you did not supply that attribute, so nothing was scored.

Check the envelope first: `status` 0 or 4 means something was served (and billed); 1, 2 and an
empty 5 mean nothing was, and nothing is charged.

## Cost
Billed per served command from a prepaid Euro wallet. `kyc_match` is a country-specific
command, so a number whose country has no configured supplier returns `status: 2` and costs
nothing. There is no idempotency key — a retry is a second charge.

## Compliance
You are the controller here. Plumma's Terms (Art. 6.2) put the obligation to obtain valid
end-user consent and establish a lawful basis squarely on you, and Art. 6.3 prohibits use for
mass surveillance, unauthorised profiling, or automated decision-making with legal effects
without human intervention. Never send real personal data to the sandbox (Art. 2.2).
