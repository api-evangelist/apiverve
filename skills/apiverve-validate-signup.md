---
name: Validate a signup with APIVerve
description: Screen a new signup's email and phone at registration — real address, not disposable, valid number — using APIVerve's validation endpoints on one key.
api: openapi/apiverve-openapi.yaml
operations: [emailvalidator, emaildisposablechecker, phonenumbervalidator]
---

# Validate a signup with APIVerve

Reject fake and throwaway signups before they land, using three APIVerve catalog endpoints on a single key.

## Auth & conventions
- Send `x-api-key: <YOUR_KEY>` on every request. Base URL `https://api.apiverve.com/v1`.
- Every response is `{ "status", "error", "data" }`. Check `status == "ok"` before reading `data`.
- Each call spends credits; watch `x-api-remaining-credits`. A `429` with `x-api-remaining-credits: 0` means out of credits (do not retry) — otherwise it is a rate limit (back off, honor `retry-after`).

## Steps
1. **Validate the email address** — `GET /v1/emailvalidator?email=<email>` (operationId `emailvalidator`). Confirms syntax and deliverability signals.
2. **Reject disposable email** — `GET /v1/emaildisposablechecker?email=<email>` (operationId `emaildisposablechecker`). If it flags a disposable/temporary provider, treat the signup as high-risk.
3. **Validate the phone number** — `GET /v1/phonenumbervalidator?phone=<phone>` (operationId `phonenumbervalidator`). Confirms the number is valid and well-formed.

## Decision
- Accept only when the email validates, is not disposable, and the phone validates.
- Never retry `400/401/403/404` — the input or key will not change between attempts. Fix the parameter or key instead.
