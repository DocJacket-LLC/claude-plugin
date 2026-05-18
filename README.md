# DocJacket — Transaction Coordination plugin for Claude

[![Plugin](https://img.shields.io/badge/Claude-plugin-blue)](https://claude.com/plugins)
[![Version](https://img.shields.io/badge/version-0.2.0-green)]()
[![License](https://img.shields.io/badge/license-MIT-lightgrey)]()

Connect DocJacket transactions, tasks, deadlines, contacts, and document checklists to Claude. Triage your pipeline, brief on any active deal, and check missing documents — all from inside Claude.

**Works with:** Claude.ai (sidebar) · Cowork · Claude Code · Claude Desktop

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

### Prerequisites

1. A DocJacket account with Owner or Admin role.
2. A bearer token from [`app.docjacket.com/settings/ai-access`](https://app.docjacket.com/settings/ai-access) — mint a new one, label it "Claude Plugin".

### Install in Claude Code / Cowork (one-time)

```bash
/plugin marketplace add DocJacket-LLC/claude-plugin
/plugin install docjacket
```

When prompted, paste your bearer token. The MCP icon in the chat input should show `docjacket` with 10 tools available.

### Install in Claude.ai (web)

1. Open [claude.com/plugins](https://claude.com/plugins).
2. Search for "DocJacket" and click **Install**.
3. Paste your bearer token in the connection dialog.

### Install in Claude Desktop (advanced)

Edit `~/Library/Application Support/Claude/claude_desktop_config.json`:

```jsonc
{
  "mcpServers": {
    "docjacket": {
      "type": "http",
      "url": "https://mcp.docjacket.com/mcp",
      "headers": {
        "Authorization": "Bearer mcp_at_...paste-here..."
      }
    }
  }
}
```

Restart Claude Desktop.

## Try it

Once installed, try:

- *"What needs my attention today?"* → fires the Daily Triage skill
- *"Find the deal at 1234 Main St"* → calls `find_transaction_by_property`
- *"What contingencies are open on the Johnson deal?"* → `list_open_contingencies`
- *"What documents are still missing on this transaction?"* → `get_missing_documents`
- `@status-reporter brief me on this week` (Cowork) → weekly briefing across active deals

## How attribution + revocation work

Every plugin call carries `X-DocJacket-Source-App: claude-desktop` + `X-DocJacket-Plugin-Version: 0.2.0`. Audit them in [Activity Log](https://app.docjacket.com/settings/ai-access/activity) — filter by source, drill into per-token activity. Revoke individual tokens from `/settings/ai-access` without affecting other AI clients (Codex, ChatGPT, etc.).

## Optional connectors

The plugin composes with Claude's Gmail, Google Calendar, Google Drive, and Slack connectors — see [`CONNECTORS.md`](CONNECTORS.md).

## Compliance + disclaimer

This plugin is read-only and does NOT provide legal advice. See [`DISCLAIMER.md`](DISCLAIMER.md) for the full scope, per-state coverage notes, and liability framing.

## Version

`0.2.0` (2026-05-18) — Daily Triage collapsed to one call (`get_next_required_actions`); 10 read tools live. Tracks DocJacket MCP server v0.9 + Plugin Cycle 3.

`0.1.0` (2026-05-17) — Initial release: 5 read tools, Daily Triage skill.

## Support

- Docs: <https://docs.docjacket.com/ai-access/claude>
- Issues: <https://github.com/DocJacket-LLC/claude-plugin/issues>
- Email: support@docjacket.com
