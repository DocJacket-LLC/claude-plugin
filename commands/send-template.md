---
description: Send an email to a party using a saved template — pick template, render with merge fields, preview, send.
---

# Send Template

Walk the user from "send the intro to the buyer on the Smith deal" through to a sent email, using their saved email templates and connected mailbox.

1. **Identify the transaction.** If the user named an address, resolve via `search_transactions`. If ambiguous (multiple matches or no clear pick), ask.
2. **Identify the template.** Call `list_email_templates` (optionally filter by `category`). If the user named a template, find the close match in the result. If still ambiguous, show 3-5 options inline and ask. If no match, ask before falling back to a generic compose.
3. **Render the template** with `render_email_template` — pass the `templateId` and `transactionId`. The response carries the rendered subject + body + the list of merge fields that resolved (or didn't).
4. **Identify the recipient.** Call `get_contacts` (or `find_contact_by_email` if the user gave you an address). Confirm the role — buyer / seller / listing agent / lender / title rep — drives which `send_*` tool to call.
5. **Show the rendered email inline** with subject, recipient name + email, and body. Flag any unresolved merge fields ("⚠️ `{{LenderName}}` didn't resolve — should I fill in manually or skip?").
6. **On confirmation, fire the matching send tool:**
   - Buyer / seller (client side) → **`send_client_update`**
   - Listing agent / lender / title rep / inspector → **`send_agent_followup`** or **`send_document_request`** (if the template is asking for docs)
   - Direct one-off to a contact who's a party on the deal but doesn't fit the above → **`send_email_to_agent`**

All `send_*` tools route through the user's connected Gmail / Outlook — no Postmark, no separate "outbox". Confirmed-then-sent.

## Example

```
📧 Drafted from "Buyer — Intro & Portal" template

To: Sarah Smith <sarah@example.com>  (Buyer on 412 Oak St)
Subject: Welcome to your 412 Oak St transaction!

Hi Sarah,

Welcome! I'm Casey, the transaction coordinator on your purchase of 412 Oak
Street. Your portal is set up...

[full body shown inline]

⚠️ One merge field didn't resolve: {{ClosingAttorneyName}} — your template
expects this but the transaction has no Closing Attorney contact yet.
Want me to send as-is (the placeholder will appear literally), skip the
sentence, or add the attorney first via add_contact_to_transaction?
```

## Don't

- **Don't auto-pick a template** when the user's request is ambiguous — show options and ask.
- **Don't paper over unresolved merge fields silently** — the email going out with a literal `{{Name}}` placeholder looks awful and damages trust. Flag it.
- **Don't send to a contact who isn't on the deal.** If the user names a recipient who's not in `get_contacts` for this transaction, either link them via `add_contact_to_transaction` first or send as a one-off (and warn).
- **Don't send without explicit confirmation** — per `execution-workflow`.
