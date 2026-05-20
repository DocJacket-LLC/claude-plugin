---
description: Show the ranked list of tasks and key dates that need attention next, across all my active deals.
---

# What's Next

Surface the urgency-ranked feed of open tasks + Key Dates across the user's organization (or a single deal if they specify one).

1. If the user named a specific transaction, resolve it via `search_transactions` and pass the resulting `transactionId` to step 2. Otherwise leave the scope org-wide.
2. Call **`get_next_required_actions`** (optional `transactionId`, default `limit: 25`). The tool already returns rows tier-sorted (overdue → due_72h → due_2w → due_4w) with a one-line `rationale` per row, plus a `summary` block with counts per tier.
3. Present grouped by urgency, leading with `overdue`. Use the `rationale` field directly — it's already TC-readable. Skip empty tiers.
4. If the response includes `breadcrumbs`, offer the most relevant follow-up the user might want — typically `get_transaction` to drill into a specific deal, or one of the `send_*` tools when the natural next step is a chase email.

## Example

```
🎯 What's next (8 items across 5 deals)

⚠️ Overdue (2)
  • Patel — property survey was due 6 days ago
  • Wong — HOA estoppel was due 3 days ago

🔥 Due in 72h (3)
  • Smith (412 Oak) — closing Friday
  • Johnson (88 Maple) — financing contingency expires Thursday EOD
  • Chen (17 Elm) — appraisal deadline Saturday

📅 Due in 2 weeks (3)
  • Smith (412 Oak) — walkthrough scheduled Wed
  • Johnson (88 Maple) — earnest money receipt due 2026-05-30
  • Foster — inspection report response due 2026-06-01
```

Then offer to act:

> _"Want me to chase Patel's survey + Wong's estoppel? Both go via `send_document_request` — I'll send from your connected Gmail once you confirm."_

## Don't

- **Don't pad the list with low-priority items** outside the 4-week horizon — they're informational, not actionable.
- **Don't reorder by category** (key dates vs tasks); the tool already merged them on urgency for a reason.
- **Don't fire `send_*` / `complete_task` / `update_key_date` without explicit confirmation** — per `execution-workflow`.
