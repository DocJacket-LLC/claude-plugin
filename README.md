<p align="left">
  <img src="assets/logo-mark-128.png" alt="DocJacket" width="96" height="96" />
</p>

# DocJacket — Transaction Coordination plugin for Claude

[![Plugin](https://img.shields.io/badge/Claude-plugin-blue)](https://claude.com/plugins)
[![Version](https://img.shields.io/badge/version-0.3.0-green)]()
[![License](https://img.shields.io/badge/license-MIT-lightgrey)]()

Connect DocJacket transactions, tasks, deadlines, contacts, and document checklists to Claude. Triage your pipeline, brief on any active deal, and check missing documents — all from inside Claude.

**Works with:** Claude.ai (sidebar) · Cowork · Claude Code · Claude Desktop

![DocJacket connector in Claude — 10 read tools, per-tool approval gates](assets/screenshots/claude-connector-tools.png)

## What you get

- **10 read tools** exposed via the DocJacket MCP server at `https://mcp.docjacket.com/mcp`
- **Daily Triage** skill — single call returns every transaction needing attention, pre-ranked overdue-first with rationales
- **Status Reporter** sub-agent (Cowork) — read-only weekly briefing across every active deal
- **`/docjacket:daily-triage`** slash command (Claude Code)

Read-only. No writes, no autonomous actions. Drafting tools (`prepare_*` / `propose_*`) and the Communication Drafter sub-agent ship in **v0.3** once the upstream MCP server reaches Phase 8.

## Tool catalog

| Tool | Purpose |
|---|---|
| `search_transactions` | Search by address, party name, MLS, status |
| `get_transaction` | Full state of one deal including joined key dates + parties |
| `get_key_dates` | Key Dates for one transaction |
| `get_open_tasks` | Open tasks org-wide or per transaction |
| `get_contacts` | Contacts in your org |
| `get_upcoming_key_dates` | Org-wide upcoming deadlines, default 14-day horizon |
| `list_open_contingencies` | Open contingencies on one transaction |
| `get_next_required_actions` | **Bundled-judgment "what to do next?"** — overdue + upcoming, pre-ranked with rationales |
| `find_transaction_by_property` | Fuzzy address-to-deal matching with confidence scores |
| `get_missing_documents` | Universal-baseline missing-doc detection for purchase deals |

## Install

**No bearer tokens. No manual config.** Paste the server URL, click Allow on the consent screen, you're done. v0.3.0 uses OAuth 2.1 + Dynamic Client Registration end-to-end.

### Prerequisites

A DocJacket account on the **Pro plan**. Connecting is free; loading tools requires Pro.

### Claude.ai (web sidebar)

1. **Settings** → **Connectors** → **+ Add custom connector**
2. Paste: `https://mcp.docjacket.com/mcp`
3. Click **Continue**, then **Allow** on the DocJacket consent screen

10 tools load. You're done.

### Claude Desktop

Same flow as Claude.ai:

1. **Settings** → **Connectors** → **+ Add custom connector**
2. Paste: `https://mcp.docjacket.com/mcp`
3. Complete the OAuth consent screen

### Claude Code / Cowork (plugin)

```bash
/plugin marketplace add DocJacket-LLC/claude-plugin
/plugin install docjacket
```

On first tool call, your browser opens to complete OAuth consent. Access + refresh tokens are stored in your local keychain — no secrets in config files.

### Claude Desktop (manual config, advanced)

If you prefer editing `~/Library/Application Support/Claude/claude_desktop_config.json` directly:

```jsonc
{
  "mcpServers": {
    "docjacket": {
      "type": "http",
      "url": "https://mcp.docjacket.com/mcp"
    }
  }
}
```

Restart Claude Desktop. The first tool call triggers OAuth discovery via the WWW-Authenticate challenge — no `Authorization` header to set, no token to mint.

## Try it

Once installed, try:

- *"What needs my attention today?"* → fires the Daily Triage skill
- *"Find the deal at 1234 Main St"* → calls `find_transaction_by_property`
- *"What contingencies are open on the Johnson deal?"* → `list_open_contingencies`
- *"What documents are still missing on this transaction?"* → `get_missing_documents`
- `@status-reporter brief me on this week` (Cowork) → weekly briefing across active deals

## How attribution + revocation work

Every plugin call carries `X-DocJacket-Source-App: claude-desktop` + `X-DocJacket-Plugin-Version: 0.3.0`. Audit them in [Activity Log](https://app.docjacket.com/settings/ai-access/activity) — filter by source, drill into per-OAuth-client activity. Revoke any connected client from `/settings/ai-access` without affecting other AI assistants (Codex, ChatGPT, etc.).

## Optional connectors

The plugin composes with Claude's Gmail, Google Calendar, Google Drive, and Slack connectors — see [`CONNECTORS.md`](CONNECTORS.md).

## Compliance + disclaimer

This plugin is read-only and does NOT provide legal advice. See [`DISCLAIMER.md`](DISCLAIMER.md) for the full scope, per-state coverage notes, and liability framing.

## Version

`0.3.0` (2026-05-18) — OAuth 2.1 + Dynamic Client Registration. Paste-URL-and-go install — no bearer tokens, no manual config. Tracks DocJacket MCP server PR #494 (HTTP 401 + WWW-Authenticate + `initialize` handshake).

`0.2.0` (2026-05-18) — Daily Triage collapsed to one call (`get_next_required_actions`); 10 read tools live. Tracks DocJacket MCP server v0.9 + Plugin Cycle 3.

`0.1.0` (2026-05-17) — Initial release: 5 read tools, Daily Triage skill.

## Support

- Docs: <https://help.docjacket.com/docs/mcp/claude>
- Issues: <https://github.com/DocJacket-LLC/claude-plugin/issues>
- Email: support@docjacket.com

## Brand assets

| File | Size | Use |
|---|---|---|
| [`assets/logo-mark.svg`](assets/logo-mark.svg) | vector | source / scalable |
| [`assets/logo-mark-32.png`](assets/logo-mark-32.png) | 32×32 | favicon, dense list rows |
| [`assets/logo-mark-64.png`](assets/logo-mark-64.png) | 64×64 | small directory cards |
| [`assets/logo-mark-128.png`](assets/logo-mark-128.png) | 128×128 | plugin manifest icon |
| [`assets/logo-mark-256.png`](assets/logo-mark-256.png) | 256×256 | medium cards |
| [`assets/logo-mark-512.png`](assets/logo-mark-512.png) | 512×512 | marketplace tile |
| [`assets/logo-mark-1024.png`](assets/logo-mark-1024.png) | 1024×1024 | hero / future-proof |
