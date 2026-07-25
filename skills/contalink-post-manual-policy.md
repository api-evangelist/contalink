---
name: Post a manual accounting policy and check the balance
description: Create a manual accounting policy (póliza manual) in Contalink and verify the resulting account and trial balances.
api: openapi/contalink-openapi-original.json
operations:
  - POST /accounting/manual-accounting-policy/
  - GET /accounting/get-account-balance/{accountnumber}/
  - GET /accounting/trial-balance/
---

# Post a manual accounting policy and check the balance

## Auth
Send your per-company API key in the `Authorization` header.
Base URL: `https://794lol2h95.execute-api.us-east-1.amazonaws.com/prod`.

## Steps
1. **Create the policy.** `POST /accounting/manual-accounting-policy/` with
   `description`, `record_date` and the `accounting_records` array (each posting
   to a chart-of-accounts account). Capture the returned policy id and `status`.
2. **Verify the account balance.** `GET /accounting/get-account-balance/{accountnumber}/`
   for an affected account to confirm the posting moved the balance.
3. **Verify the trial balance.** `GET /accounting/trial-balance/` with
   `start_date`/`end_date` or `period` to confirm the balanza de comprobación
   reflects the new policy.

## Rules
- Dates use `YYYY-MM-DD`.
- No idempotency key exists; a retried `POST` creates a second policy. Fetch the
  policy (`GET /accounting/manual-accounting-policy/{policyid}/`) before retrying,
  and use `PATCH`/`DELETE` on that id to correct rather than re-posting.
- Responses carry a `{status, message}` envelope; check `status`.
