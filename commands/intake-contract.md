---
description: Intake a contract PDF and build the full transaction in one conversation.
---

# Intake Contract

Walk the TC from "I just got a signed contract" to "the deal is fully set up in DocJacket" without leaving chat. This command invokes the `contract-intake` skill, which handles the full Phase 1-5 flow.

## What the TC provides

- A contract PDF (attached to the conversation)
- Optional context: state ("Florida"), side ("buyer-side" / "seller-side"), or document type ("listing agreement")

## What you do

Per the `contract-intake` skill recipe:

1. **Upload** — preferred path is two-step: `request_upload_url({ filename })` returns a `uploadUrl` + `uploadId`; PUT the PDF bytes to `uploadUrl` (outside the LLM); then `kick_off_extraction({ uploadId, filename, state?, side?, documentTypeHint? })`. This keeps PDF bytes out of chat context (a 5 MB PDF base64-encoded would burn ~6.7 MB of tokens for the rest of the conversation). For small PDFs or clients that can't PUT to a presigned URL, fall back to `upload_document_for_extraction({ fileBase64, filename, ... })`.
2. **Poll** — call `get_extraction_results` every 2-3 seconds until `status: "complete"`. Surface a "still working…" update if the wait exceeds 15 seconds. Cap at 10 minutes total.
3. **Confirm** — paraphrase the extracted fields (property, parties, dates, financials) and ask the TC to confirm or edit. Don't dump JSON.
4. **Apply** — call `apply_extraction` with any user edits as a flat `overrides` map. The response gives you the new `transactionId` — pin every follow-up call to it.
5. **Build the timeline** — propose computed dates (EMD, inspection, appraisal, financing, walkthrough) based on contract terms. On approval, call `add_key_dates_batch`.
6. **Send intros** — `render_email_template` first, present drafts, then `send_client_update` + `send_email_to_agent`.
7. **Schedule reminders** — `create_reminder` for the deadlines the TC names ("nudge the agent 2 days before EMD" → one call).
8. **Confirm** — call `get_intake_status` and present the final summary with the portal link from `get_portal_link`.

## Failure recovery

If anything fails mid-flow, call `get_intake_status(transactionId)`. The response's `missingRecommendedSteps[]` tells you exactly what to retry — and which tool to retry with. Don't restart from Phase 1.

## Anti-patterns

Per the `contract-intake` skill:
- Don't skip Phase 3 confirmation, even when extraction looks clean.
- Don't pre-populate `overrides` with every field — only the keys the TC actually edited.
- Don't fabricate dates the contract doesn't state.
- Don't call `apply_checklist` after `apply_extraction` — the apply already runs the default state/type/side template.
- Don't loop polling forever — cap at 10 minutes wall-clock.
