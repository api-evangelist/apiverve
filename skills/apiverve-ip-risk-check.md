---
name: IP risk & geolocation check with APIVerve
description: Score an inbound IP for location and abuse reputation using APIVerve's IP lookup and blacklist endpoints.
api: openapi/apiverve-openapi.yaml
operations: [iplookup, ipblacklistlookup]
---

# IP risk & geolocation check with APIVerve

Locate and risk-score an inbound IP from two APIVerve endpoints on one key.

## Auth & conventions
- Send `x-api-key: <YOUR_KEY>`. Base URL `https://api.apiverve.com/v1`.
- Responses are `{ "status", "error", "data" }`; check `status == "ok"` first.
- Credit-metered; interpret `429` via `x-api-remaining-credits` (0 = out of credits, else rate limit — honor `retry-after`).

## Steps
1. **Geolocate & profile** — `GET /v1/iplookup?ip=<ip>` (operationId `iplookup`). Country/region/city and network details.
2. **Reputation** — `GET /v1/ipblacklistlookup?ip=<ip>` (operationId `ipblacklistlookup`). Check the IP against known blacklists.

## Decision
- Combine an unexpected geo with a blacklist hit to gate or step up (MFA / manual review).
- Both are read-only GETs — idempotent and repeatable; nothing to undo.
