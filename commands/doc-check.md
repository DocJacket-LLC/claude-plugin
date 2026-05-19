---
description: Run a document completeness audit for one transaction or across all active deals.
---

# Document Completeness Check

## With a transaction specified

If the user names a transaction or has one in context:

1. `get_missing_documents` for that transaction
2. Show received-vs-missing
3. For each missing doc, name which recipient role typically provides it (lender → loan_approval / closing_statement; title co → title_commitment / survey; etc.)
4. Offer to send chase emails via the `follow-up-drafting` skill (`send_document_request`)

## No transaction specified — org-wide

1. `list_active_transactions` (no party / key-date expansion — minimal)
2. For each active transaction, `get_missing_documents`
3. Show a summary: transactions with complete docs vs incomplete
4. **Highlight any transaction closing within 14 days that has missing docs** — that's the urgent set

## Presentation

```
📁 Document Completeness — 12 active deals

✅ Complete (5)
  412 Oak St (Smith) — closing 6/15
  88 Maple Ave (Johnson) — closing 6/22
  17 Elm Dr (Chen) — closing 7/05
  ...

⚠️ Incomplete (7)
  • Wong (2412 Bayview) — closing 6/30 — missing: HOA estoppel
  • Patel (1247 Pine) — closing 6/18 — missing: survey, wire instructions
  ...

🚨 Urgent (closing < 14 days + missing docs) (2)
  • Wong (6/30, 11 days) — HOA estoppel
  • Patel (6/18, 5 days) — survey, wire instructions

Want me to send chases for the urgent items?
```

On user yes, per `execution-workflow`, call `send_document_request` for each (bundling items going to the same recipient into one email). Confirm bundle scope before firing.

## Don't

- **Don't include closed or cancelled deals.** `list_active_transactions` already filters; just don't override it.
- **Don't speculate which docs are missing.** Use `get_missing_documents`.
