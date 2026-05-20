---
description: Review new and recent intake-form submissions — find what came in, see what each contains, decide what to do with it.
---

# Check Form Submissions

Surface incoming intake-form submissions across the user's active forms, summarize what each one is asking the TC to do, and offer the obvious next moves (link to a transaction, send a follow-up, ignore).

1. Call **`list_form_links`** to enumerate the org's intake forms. If the user named a specific form, narrow to that one; otherwise carry all.
2. For each relevant form, call **`list_form_submissions`** (default to `status: "new"` if the user just said "what's new?", otherwise pull recent across statuses).
3. For each submission worth surfacing (new, or matches the user's filter), call **`get_form_submission`** to see the full field values. Skip this for any submission you already have enough metadata to summarize.
4. **Group by form.** Within a form, lead with newest. Show: submitter name / email if captured, submission date, the 2-3 most identifying fields (typically property address + a contact). Pre-classify the submitter when possible (buyer-side / seller-side / referral) using whatever role-style field the form has.
5. **Offer next moves per submission:**
   - "Spin this into a transaction" → point at the `/docjacket:intake` command (which runs the `contract-intake` skill).
   - "Reply to the submitter" → `send_agent_followup` if the submitter is an agent, otherwise `send_email_to_agent` for a one-off.
   - "I've handled this" → `log_activity` to mark it acted-on (the submission status itself is managed in the DocJacket UI, not via MCP yet).
6. If the user wants to know what fields the form is asking for (common when triaging a stale form): call **`get_form_definition`** and summarize the field list.

## Example

```
📋 Form submissions (4 new across 2 forms)

▶ Buyer Intake — Spring 2026 (3 new)

  1. Sarah Lin — 412 Oak Street — 2026-05-19 09:42
     Pre-approved Wells Fargo · Cash backup · 30-day close target
     → Spin into a transaction? (uses /docjacket:intake)

  2. Tomas Reyes — 88 Maple Ave — 2026-05-18 16:11
     Pre-qual letter attached · Wants 45-day close · Inspection contingency required
     → Spin into a transaction?

  3. Anonymous (no contact info) — empty submission — 2026-05-18 03:22
     ⚠️ Looks like a test or bot. Want me to log it dismissed?

▶ Listing Intake (1 new)

  4. Mike Park (listing agent) — 17 Elm Street — 2026-05-19 11:08
     Reaching out about a referral — already has buyer interest
     → Reply via send_agent_followup?
```

## Don't

- **Don't auto-spin a submission into a transaction.** `contract-intake` is its own multi-step workflow and the user should drive it.
- **Don't open every submission with `get_form_submission`** if you only need the summary fields — that's 1 call per submission and 50 unread submissions = 50 extra round-trips. Filter to "new + interesting" first.
- **Don't claim the submission status changed in DocJacket** — there's no MCP write tool for that yet; the user has to mark it processed in the web UI.
- **Don't reply or log without explicit confirmation** — per `execution-workflow`.
