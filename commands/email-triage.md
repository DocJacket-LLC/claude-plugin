---
description: Match my inbox to my active deals and surface what needs action.
---

# Email Triage

Run the full inbox-to-transaction matching workflow per the `email-triage` skill recipe.

## Required

This command needs the Gmail or Outlook connector active in Cowork. If neither is connected:

> _"I need a Gmail or Outlook connection to triage your inbox. Add it in Cowork settings, then run `/docjacket:email-triage` again."_

## The recipe

1. `list_active_transactions` (with `include_parties: true`, `include_key_dates: true`) — once, cached for the session.
2. Read recent unread mail via the email connector (last 24-48 hours).
3. For each email: match by sender → `find_contact_by_email`. Fall back to `search_transactions` by address / party name.
4. For each attachment: `classify_document` + (if matched to a transaction) `get_missing_documents` to flag gap-fillers.
5. Group by transaction. Skip newsletters / promotions / noise.
6. Surface deadline proximity for any matched deal with key dates in the next 7 days.
7. Ask the user for decisions, bundled per transaction.

## Presentation

Lead with what needs attention. One section per transaction. For attachments that fill a missing-doc gap, say so explicitly.

For unmatched emails, list them in a final "Unmatched" group; ask the user if any relate to a new or existing deal.

## On approval — chase / draft follow-up

If the user approves drafting a chase email or status update, switch to the `follow-up-drafting` skill — compose, confirm, fire the matching `send_*` tool. Per `execution-workflow`, the chat is the approval gate.

## On approval — file an attachment

There is no `upload_document` MCP tool yet. After the user approves filing:

> _"I'd file the inspection report to the Smith deal, but file-on-approval via the AI isn't available yet. Upload it manually in DocJacket → Documents → Smith. Want me to create a task to track that?"_

On yes, call `create_tasks` to add an "Upload [filename]" task to the transaction.
