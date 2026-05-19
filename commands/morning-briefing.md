---
description: Brief me on what needs attention across all my active deals.
---

# Morning Briefing

Walk through every active transaction and surface what needs the user's attention today.

1. Call `list_active_transactions` with `include_parties: true` and `include_key_dates: true` — cache the result for the conversation.
2. Call `get_next_required_actions` to get the org-wide ranked task + key-date list (overdue → due-72h → due-2w → due-4w).
3. For each transaction with anything in the first three urgency tiers OR closing within 14 days, call `get_missing_documents` and `get_checklist_status` so you can mention gaps.
4. If the Gmail / Outlook / Calendar connectors are present:
   - Check the inbox for unread threads from any party on the active deals (use the `email-triage` skill recipe)
   - Check today + tomorrow's calendar for closings / inspections / walkthroughs

Present as a concise briefing:

```
🌅  Tuesday, May 19

📍 Today
  • 412 Oak St — closing 3pm at ABC Title — walkthrough complete, CD on file
  • 88 Maple — inspection 10am (Mike Park's notes: HVAC concern from last visit)

⏰ This week
  • Smith (412 Oak) — closing Friday
  • Johnson (88 Maple) — financing contingency expires Thursday EOD
  • Chen (17 Elm) — appraisal deadline Saturday

⚠️ Overdue (2)
  • Patel — property survey overdue 6 days — chase the title co?
  • Wong — HOA estoppel overdue 3 days — chase the management co?

📧 Needs response (3 unread from parties)
  • Mike Park (Smith, buyer agent) — "Inspection report"
  • ABC Title (Smith) — "Final HUD"
  • Casey Lender (Johnson) — "Loan #12345 conditions"

📁 Missing docs across all deals (4)
  • Smith — Survey
  • Johnson — Loan commitment letter, Wire instructions
  • Wong — HOA estoppel
```

Lead with the most urgent items. Don't list deals with nothing demanding attention.

Offer to act:

> _"Want me to send chases for Patel's survey + Wong's estoppel? Both go via `send_document_request` — I'll send straight from your connected Gmail once you say yes."_

Per `execution-workflow`, wait for explicit confirmation before firing any `send_*` / `create_*` / `update_*` tool.
