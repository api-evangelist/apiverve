---
name: Domain intelligence sweep with APIVerve
description: Profile a domain's DNS, ownership and TLS posture in one pass using APIVerve's DNS, WHOIS and SSL endpoints.
api: openapi/apiverve-openapi.yaml
operations: [dnslookup, whoislookup, sslchecker]
---

# Domain intelligence sweep with APIVerve

Gather a domain's records, registration and certificate health from three APIVerve endpoints on one key.

## Auth & conventions
- Send `x-api-key: <YOUR_KEY>`. Base URL `https://api.apiverve.com/v1`.
- Responses are `{ "status", "error", "data" }`; branch on `status == "ok"`.
- Credit-metered; a `429` with `x-api-remaining-credits: 0` is credit exhaustion (stop), otherwise a rate limit (back off with `retry-after`).

## Steps
1. **DNS records** — `GET /v1/dnslookup?domain=<domain>` (operationId `dnslookup`). Pull A/AAAA/MX/NS/TXT records.
2. **Registration** — `GET /v1/whoislookup?domain=<domain>` (operationId `whoislookup`). Registrar, creation/expiry dates, ownership where public.
3. **TLS / certificate** — `GET /v1/sslchecker?domain=<domain>` (operationId `sslchecker`). Certificate validity, issuer and expiry.

## Notes
- All three are read-only GETs — safe to re-run; there is no state to change and nothing to reverse.
- For many domains at once, use batch requests (up to 200 lookups per call for a single endpoint).
