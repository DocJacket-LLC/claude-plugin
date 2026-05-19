---
description: Get the portal link for a transaction and optionally send an email to share it with a party.
---

# Share Portal Link

1. **Identify the transaction.** If ambiguous, ask. (Search via `search_transactions` if the user named an address.)
2. Call **`get_portal_link`** to retrieve the URL + calendar feed.
3. **Handle the result:**
   - `PORTAL_NOT_FOUND` → tell the user there's no active portal link for this deal yet. Creating one is done in DocJacket → Portal (creating links via the AI is a future feature).
   - Found → show the URL + calendar feed URL inline.
4. **Ask who to share it with** (buyer, seller, agent, lender). If the answer is one of the parties already on the deal, look them up via `find_contact_by_email` or `get_contacts`.
5. **Draft a brief intro email.** Include both the portal URL and the calendar feed URL. For a buyer / seller → `send_client_update`. For an agent → `send_agent_followup`.
6. Show the draft inline, ask the user to confirm. On yes, fire the `send_*` tool. The email leaves from the user's connected Gmail / Outlook.

## Example

```
🔗 Portal link for 412 Oak St (Smith)

URL: https://app.docjacket.com/portal/abc123def456
Calendar feed: https://app.docjacket.com/api/calendar/.../feed.ics
Created: 2026-05-15 by Casey  ·  Expires: never

Want me to send Sarah (buyer) an intro email with the link? She can subscribe
to the calendar feed in Apple Calendar / Google Calendar so all the deadlines
show up automatically.
```

## Don't

- **Don't paste the link in chat as the only delivery.** Portal URLs are scoped to a recipient; better to deliver via an email the user reviewed.
- **Don't create a new portal link via the AI.** No tool for that yet.
- **Don't share a link to a party on a different deal.** Each transaction has its own link.
- **Don't send without explicit confirmation** — per `execution-workflow`.
