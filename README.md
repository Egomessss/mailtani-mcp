# Mailtani MCP Server

> Manage your email marketing directly from Claude, Cursor, or any MCP-compatible AI agent.

The Mailtani MCP server exposes your contacts, lists, segments, campaigns, emails, tags, custom fields, and event tracking as native AI tools — so your agent can send campaigns, enroll contacts, and track events without leaving the chat.

---

## What you can do

| Category | Tools |
|---|---|
| **Contacts** | List, create (or upsert), update, delete |
| **Lists** | List all, create, add contacts, remove contact |
| **Segments** | List all, create with conditions, get matching contacts |
| **Campaigns** | List, create broadcast, create sequence, create flow (all as drafts) |
| **Emails** | List sent emails, send one-off transactional email |
| **Tags** | List all, create, delete |
| **Custom Fields** | List all, create |
| **Events** | Track a custom event (triggers flows) |

### Example prompts you can use

```
"Create a broadcast campaign called 'Q3 Newsletter' targeting my 'Leads' list."

"Create a contact for john@acme.com, tag him as VIP, and send him the onboarding email."

"Track a purchase_completed event for sarah@example.com with plan=pro."

"Show me all contacts added in the last 7 days."

"Set up a 3-step welcome sequence for new signups with delays of 0, 3, and 7 days."

"Create a flow that sends an email whenever a contact is added to my project."
```

---

## Authentication

The MCP server uses the same API key system as the Mailtani REST API.

**Every request requires two headers:**

| Header | Value |
|---|---|
| `Authorization` | `Bearer mk_YOUR_API_KEY` |
| `X-Project-Id` | Your project ID (found in Settings → API) |

Generate an API key at **Settings → API Keys** inside your Mailtani account.

---

## Setup

### Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (Mac) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "mailtani": {
      "type": "http",
      "url": "https://mailtani.com/mcp",
      "headers": {
        "Authorization": "Bearer mk_YOUR_API_KEY",
        "X-Project-Id": "YOUR_PROJECT_ID"
      }
    }
  }
}
```

Restart Claude Desktop. You will see a hammer icon confirming the server connected.

### Claude Code

Run in your terminal:

```bash
claude mcp add --transport http mailtani https://mailtani.com/mcp \
  --header "Authorization: Bearer mk_YOUR_API_KEY" \
  --header "X-Project-Id: YOUR_PROJECT_ID"
```

### Cursor

Add to your Cursor MCP settings (`Cursor → Settings → MCP`):

```json
{
  "mailtani": {
    "type": "http",
    "url": "https://mailtani.com/mcp",
    "headers": {
      "Authorization": "Bearer mk_YOUR_API_KEY",
      "X-Project-Id": "YOUR_PROJECT_ID"
    }
  }
}
```

### Codex

Add to `~/.codex/config.json`:

```json
{
  "mcpServers": {
    "mailtani": {
      "type": "http",
      "url": "https://mailtani.com/mcp",
      "headers": {
        "Authorization": "Bearer mk_YOUR_API_KEY",
        "X-Project-Id": "YOUR_PROJECT_ID"
      }
    }
  }
}
```

### Any MCP-compatible client

```
Endpoint:  POST https://mailtani.com/mcp
Protocol:  MCP over HTTP (JSON-RPC 2.0)
Auth:      Authorization: Bearer mk_...
Scope:     X-Project-Id: <id>
Rate limit: 30 requests / minute
```

---

## Tool reference

### Contacts

| Tool | Description |
|---|---|
| `list-contacts` | List contacts with optional search, list/tag/status filter, pagination |
| `create-contact` | Create or upsert a contact by email; optionally assign lists and tags |
| `update-contact` | Update contact fields, lists, and tags by contact ID |
| `delete-contact` | Permanently delete a contact by ID |

### Lists

| Tool | Description |
|---|---|
| `list-lists` | List all contact lists with contact counts |
| `create-list` | Create a new contact list |
| `add-contact-to-list` | Add up to 500 contacts to a list at once |
| `remove-contact-from-list` | Remove a single contact from a list |

### Segments

| Tool | Description |
|---|---|
| `list-segments` | List all segments with contact counts |
| `create-segment` | Create a segment with filter conditions (max 50) |
| `get-segment-contacts` | Get paginated contacts matching a segment |

### Campaigns

Campaigns are always created as **drafts**. Open the Mailtani app to review and activate them.

| Tool | Description |
|---|---|
| `list-campaigns` | List campaigns filtered by type or status |
| `create-broadcast` | Create a one-time email blast draft; optionally target lists and segments |
| `create-sequence` | Create a multi-step drip sequence draft with per-step delays |
| `create-flow` | Create a visual automation flow draft triggered by contact behaviour |

### Emails

| Tool | Description |
|---|---|
| `list-emails` | List sent emails, optionally filtered by contact |
| `send-email` | Send a one-off transactional email to any address |

### Tags

| Tool | Description |
|---|---|
| `list-tags` | List all tags with contact counts |
| `create-tag` | Create a new tag |
| `delete-tag` | Delete a tag by ID |

### Custom Fields

| Tool | Description |
|---|---|
| `list-custom-fields` | List all custom fields |
| `create-custom-field` | Create a custom field (idempotent by name) |

### Events

| Tool | Description |
|---|---|
| `track-event` | Track a custom event for a contact; persists properties and triggers flows |

---

## Security

- All operations are scoped to your project via `X-Project-Id` — no tool can read or modify another project's data
- API keys are SHA-256 hashed at rest; plaintext is never stored
- Rate limited to 30 requests per minute
- Daily email send limits are enforced per your plan

---

## Links

- [Mailtani](https://mailtani.com)
- [REST API docs](https://mailtani.com/docs/api)
- [MCP server docs](https://mailtani.com/docs/mcp)
- [Get an API key](https://mailtani.com/settings/api)
