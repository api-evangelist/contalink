---
name: Upload a CFDI and verify its status
description: Upload a fiscal-document XML to Contalink and poll its processing and fiscal status.
api: openapi/contalink-openapi-original.json
operations:
  - POST /invoices/upload/
  - GET /invoices/check-status/{uuid}/
  - GET /invoices/list/
---

# Upload a CFDI and verify its status

## Auth
Send your per-company API key in the `Authorization` header.
Base URL: `https://794lol2h95.execute-api.us-east-1.amazonaws.com/prod`.

## Steps
1. **Upload.** `POST /invoices/upload/` with `name` and the base64/XML `xml`
   payload. This is processed **asynchronously** — the response `result`/`status`
   acknowledges receipt, not final processing.
2. **Check fiscal status.** `GET /invoices/check-status/{uuid}/` with the CFDI
   UUID to validate whether the document is active, canceled, or its payment
   situation.
3. **Confirm it landed.** `GET /invoices/list/` filtered by `rfc` and the date
   range to confirm the document now appears in the fiscal-document listing.

## Rules
- Because upload is asynchronous, poll `check-status` rather than assuming the
  document is booked immediately.
- No idempotency key exists; re-uploading the same XML can create a duplicate —
  check the listing before retrying.
- Dates use `YYYY-MM-DD`; the UUID is the CFDI fiscal folio.
