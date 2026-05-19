<!--
  Generated from plugins/shared/skills/daily-triage.md — do not hand-edit.
  Update the canonical file and re-sync. See plugins/README.md for the rule.
  Generator: hand-curated v1 (per spec §3.3). Last sync: 2026-05-18.
-->

---
name: daily-triage
title: Daily Triage
purpose: Find every transaction that needs attention today and surface the next action per deal, grouped by urgency.
connector: docjacket
tools_used:
  - DocJacket.get_next_required_actions
required_scopes: [read]
version: 0.2.0
---

# Daily Triage

## When to invoke

Trigger on any variation of:

- "what needs my attention today?"
- "give me a TC briefing"
- "what's overdue?"
- "what's at risk this week?"
- "morning briefing" / "morning triage"
- "what should I work on first?"

## Workflow

**One call. The tool returns a pre-ranked, pre-bucketed feed.**

1. Call `DocJacket.get_next_required_actions` with `limit: 75`. (Optional: pass `transactionId` to scope to a single deal.)
2. The response is already sorted overdue-first and tagged with `urgency` per row — group by the `urgency` field as-is.

**Do not fan out per transaction** — the previous v0.1 workflow called `search_transactions` then `get_open_tasks` + `get_key_dates` per active deal then sorted client-side. That's been replaced by this single call.

## Output shape

```
🔥 Overdue (3)
  • 1234 Main St — Inspection Deadline was due 2 days ago.
  • 5678 Oak Ave — Task "Order title" is 5 days overdue.
  • 9012 Pine Rd — Financing Deadline was due 1 day ago.

⏰ Due within 72 hours (5)
  • 2468 Elm — Closing Date due in 2 days.
  • ...

📅 Due within 2 weeks (12)
  • ...
```

Use the `urgency` field to decide the section header:

| `urgency` | Section header | Emoji |
|---|---|---|
| `overdue` | Overdue | 🔥 |
| `due_72h` | Due within 72 hours | ⏰ |
| `due_2w`  | Due within 2 weeks | 📅 |
| `due_4w`  | Due within 4 weeks | 📋 |

Render the `rationale` string verbatim. Prefix each row with the `transactionAddress` followed by an em-dash.

If `summary.totalCount === 0`, render: `✅ All clear — no items need attention right now.`

## Anti-patterns

- **Don't** call `search_transactions` + `get_open_tasks` + `get_key_dates` separately and re-sort. The server-side bundled-judgment tool does this in one shot.
- **Don't** call `get_transaction` to look up addresses — `transactionAddress` is on each row.
- **Don't** call `get_next_required_actions` per transaction in a loop. One org-wide call covers everything.
- **Don't** summarize completed or approved items.
- **Don't** propose writes from this skill — triage is read-only.

## Skill version

`v0.2.0` — single-call workflow via `get_next_required_actions`.
