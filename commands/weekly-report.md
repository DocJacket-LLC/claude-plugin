---
description: Summarize the week's activity across all transactions — for the broker, the team, or the user's own records.
---

# Weekly Report

1. `list_active_transactions` (include parties + key dates) — once, cached.
2. `get_next_required_actions` with no `transactionId` (org-wide) — ranked overdue + upcoming items.
3. Optionally pull each transaction's `get_open_tasks` for activity-level rollup.

## Compile

Report sections:

- **Closing this week** — transactions with a closing date in the next 7 days
- **Closed this week** — closing dates in the last 7 days (heuristic — doesn't distinguish actual close from delays; note this caveat)
- **New files** — transactions whose `updatedAt` is in the last 7 days (heuristic proxy for "recently created"; note caveat)
- **Stuck deals** — deals with overdue key dates OR no activity in 7+ days
- **Upcoming closings** — closing dates in the next 14 days
- **Stats** — total active, total closing this month, total volume (sum of `purchasePrice` on closings this month)

## Presentation

```
📊 Weekly Report — Week of May 13-19, 2026

🏁 Closing this week (3)
  • 412 Oak St (Smith) — Friday 6/15 — $485k
  • 88 Maple Ave (Johnson) — Friday 6/15 — $612k
  • 17 Elm Dr (Chen) — Friday 6/15 — $720k

📈 Stats
  • 18 active transactions
  • 3 closing this week ($1.8M total volume)
  • 7 closing this month ($4.2M total volume)

🚨 Stuck (no activity 7+ days) (2)
  • Patel (1247 Pine) — last updated 5/10 — survey overdue 6 days
  • Wong (2412 Bayview) — last updated 5/11 — HOA estoppel overdue 3 days

📅 Upcoming closings (next 14 days)
  • Smith — 6/15
  • Johnson — 6/15
  • Chen — 6/15
  • Wong — 6/30
```

## Tone

This is a report a user could forward to their broker. Clean, scannable, no chat-style filler. Lead with this-week's closings.

## Optional follow-on

Offer to `save_status_summary` on stuck deals so the user has a note on file:

> _"Want me to save a status recap on the Patel + Wong deals? It'll show up in the Notes card with a `Source: AI` tag."_

On yes, per `execution-workflow`, fire `save_status_summary` per transaction.

## Caveats

If using heuristic proxies (closing-date for "closed this week", `updatedAt` for "stuck"), say so:

> _Note: "Closed this week" reflects closing dates that fell this week — doesn't distinguish actual close vs delayed. "Stuck" uses last-updated timestamps as a proxy for no recent activity._

## Don't

- **Don't include unrelated workspace data** (e.g. unsigned NDAs from a different vertical). Stay focused on active transactions.
- **Don't speculate on revenue / commission.** Report transaction prices; commission math isn't exposed via the current tools.
