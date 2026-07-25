---
name: Reconcile an invoice payment against a bank movement
description: Find a CFDI invoice, record the bank movement that paid it, and reconcile the two in Contalink.
api: openapi/contalink-openapi-original.json
operations:
  - GET /invoices/list/
  - POST /treasury/bank-transactions/
  - POST /conciliation/create/
---

# Reconcile an invoice payment against a bank movement

Use this flow to match a received payment to a CFDI invoice in Contalink.

## Auth
Send your per-company API key in the `Authorization` header on every request.
Base URL: `https://794lol2h95.execute-api.us-east-1.amazonaws.com/prod`.

## Steps
1. **Find the invoice.** `GET /invoices/list/` with `rfc`, `start_date`, `end_date`
   and `document_type` filters to locate the CFDI. Page with the `page` query
   parameter if the result set is large. Capture the invoice's `invoice_id`.
2. **Record the bank movement.** `POST /treasury/bank-transactions/` with the
   `bank`, `date`, `deposit` (or `withdrawal`), `description` and `reference`
   fields to book the deposit that settled the invoice. Capture the returned
   `transaction_bank` id.
3. **Reconcile.** `POST /conciliation/create/` with `amount`, `bank_account`,
   `invoice_id`, `payment_date` and `payment_form` to link the payment to the
   invoice. Confirm `status` in the response.

## Rules
- Dates use `YYYY-MM-DD`.
- There is no idempotency-key header; do not blindly retry `POST` calls — a retry
  creates a duplicate bank movement. On a timeout, re-`GET` before re-posting.
- Responses carry a `{status, message}` envelope; check `status` before proceeding.
