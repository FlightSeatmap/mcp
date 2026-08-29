# FlightSeatMap MCP

Seat maps, seat ratings, traveller reviews and seat alerts for 150+ airlines,
as a remote MCP server your AI assistant can call.

Ask "which seat should I pick on QF1?" and get the real cabin layout, the free
seats ranked against what you care about, and what other travellers said about
the one you're considering.

- **Server:** `https://mcp.flightseatmap.com/mcp`
- **Transport:** Streamable HTTP
- **Auth:** none for the read-only tools; OAuth 2.1 for search and alerts
- **Docs:** <https://flightseatmap.com/mcp>

Nothing needs installing. The server is hosted; you point your client at the URL.

## Install

### Claude Code

```bash
claude mcp add --transport http flightseatmap https://mcp.flightseatmap.com/mcp
```

### Cursor

Install this plugin from the marketplace, or add the server directly in
`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "flightseatmap": {
      "type": "streamable-http",
      "url": "https://mcp.flightseatmap.com/mcp"
    }
  }
}
```

### VS Code

```bash
code --add-mcp '{"name":"flightseatmap","type":"http","url":"https://mcp.flightseatmap.com/mcp"}'
```

### Claude Desktop

Settings → Connectors → Add custom connector, then paste the server URL.

## Tools

Six tools are free and need no account:

| Tool | What it does |
|---|---|
| `get_seatmap` | The cabin layout for a flight: rows, cabins, which seats are free |
| `find_best_seats` | Free seats ranked against window, aisle, exit row, extra legroom, quiet zone, bulkhead, front, rear |
| `get_seat_info` | One seat in detail — pitch, recline, power, galley and lavatory proximity |
| `interactive_seat_finder` | A good default when the traveller hasn't said what they want yet |
| `get_seat_reviews` | Traveller reviews, with ratings and per-seat comments |
| `discover_more_flight_tools` | Related flight and travel MCP servers |

Four need a signed-in account, over OAuth:

| Tool | What it does |
|---|---|
| `search_flight` | Fetch fresh data for a flight not yet in the database |
| `create_seat_alert` | Email the traveller when a matching seat frees up |
| `list_seat_alerts` | The traveller's alerts, with departure countdowns |
| `delete_seat_alert` | Remove an alert |

Seat map, review and alert results render as interactive
[MCP Apps](https://modelcontextprotocol.io) widgets in clients that support them.

## Authentication

OAuth 2.1 with PKCE and dynamic client registration — there is no API key to
request, and no form to fill in. Your client discovers everything it needs from
the 401 challenge.

- Protected resource metadata: `https://mcp.flightseatmap.com/.well-known/oauth-protected-resource/mcp`
- Authorization server metadata: `https://mcp.flightseatmap.com/.well-known/oauth-authorization-server`
- Written walkthrough: <https://flightseatmap.com/auth.md>

Scopes are `read`, `write` and `search`. Tools request only the scope they need.

## What's in this repo

| Path | Purpose |
|---|---|
| `plugin.json` | [Agent Plugins](https://agent-plugins.org) manifest — loads in Cursor unchanged |
| `mcp.json` | Server declaration for plugin-aware clients |
| `server.json` | Manifest for the [official MCP Registry](https://modelcontextprotocol.io/registry) |
| `skills/` | Agent skills describing how to use the tools well |

The seat data, the API and the site are a separate, closed-source product. This
repo is the connection glue, and it is MIT licensed.

## Notes

- Availability reflects the seat map, not the airline's live booking inventory.
  A seat shown as free may already be gone.
- Seat maps are keyed on flight number (`QF1`, `BA117`, `UA900`), not route. A
  date helps, because aircraft swap between dates.
- Extra-legroom and exit-row seats usually carry a fee, and exit rows carry age
  and mobility requirements.

## Support

<support@flightseatmap.com> · [Privacy](https://flightseatmap.com/privacy) ·
[Terms](https://flightseatmap.com/terms)
