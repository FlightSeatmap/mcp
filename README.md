# FlightSeatMap MCP

Seat maps, seat ratings, traveller reviews and seat alerts for 117 airlines,
as a remote MCP server your AI assistant can call.

Ask "which seat should I pick on QF1?" and get the real cabin layout, the free
seats ranked against what you care about, and what other travellers said about
the one you're considering.

- **Server:** `https://mcp.flightseatmap.com/mcp`
- **Transport:** Streamable HTTP
- **Auth:** OAuth 2.1 sign-in for every tool. A free account covers all but `search_flight`, seat alerts and flight alerts
- **Docs:** <https://flightseatmap.com/mcp>

Nothing needs installing. The server is hosted; you point your client at the URL.

## Install

### Claude Code

Install the plugin, then let the setup skill pick your first seat:

```
/plugin marketplace add FlightSeatmap/mcp
/plugin install flightseatmap@flightseatmap
/flightseatmap:setup
```

Or add only the server:

```bash
claude mcp add --transport http flightseatmap https://mcp.flightseatmap.com/mcp
```

### Cursor

[Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=flightseatmap&config=eyJ1cmwiOiJodHRwczovL21jcC5mbGlnaHRzZWF0bWFwLmNvbS9tY3AifQ==)
(one click), install the plugin from the Cursor marketplace, or add the
server directly in `~/.cursor/mcp.json`:

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

### Grok Bot

Grok Bot uses your Cursor plugin library. Install the FlightSeatMap plugin
from Plugins, then click **Authenticate**. Or ask the bot in chat:

> Add a custom MCP server called flightseatmap at https://mcp.flightseatmap.com/mcp

Click **Add it**, then **Authorize** on the connect card and sign in to FlightSeatMap.

### Grok (grok.com)

Settings → Connectors → Add custom connector, then paste the server URL.

### VS Code

```bash
code --add-mcp '{"name":"flightseatmap","type":"http","url":"https://mcp.flightseatmap.com/mcp"}'
```

### Claude Desktop

Settings → Connectors → Add custom connector, then paste the server URL.

## Tools

Nine tools work with a free account:

| Tool | What it does |
|---|---|
| `get_seatmap` | The cabin layout for a flight: rows, cabins, which seats are free |
| `find_best_seats` | Free seats ranked against window, aisle, exit row, extra legroom, quiet zone, bulkhead, front, rear |
| `get_seat_info` | One seat in detail — pitch, recline, power, galley and lavatory proximity |
| `interactive_seat_finder` | A good default when the traveller hasn't said what they want yet |
| `get_seat_reviews` | Traveller reviews, with ratings and per-seat comments |
| `submit_seat_review` | Post the traveller's own review of a flight and seat (one per flight) |
| `vote_review` | Mark a review as helpful or not helpful |
| `get_airline_info` | An airline's baggage fees, carry-on limits, in-flight WiFi and fleet |
| `discover_more_flight_tools` | Related flight and travel MCP servers |

Seven need a paid plan:

| Tool | What it does |
|---|---|
| `search_flight` | Fetch fresh data for a flight not yet in the database |
| `create_seat_alert` | Email the traveller when a matching seat frees up |
| `list_seat_alerts` | The traveller's alerts, with departure countdowns |
| `delete_seat_alert` | Remove an alert |
| `create_flight_alert` | Email the traveller when the aircraft or schedule changes, or the flight is delayed |
| `list_flight_alerts` | The traveller's flight alerts |
| `delete_flight_alert` | Remove a flight alert |

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
| `mcp.json` | Server declaration, Agent Plugins form |
| `.claude-plugin/plugin.json` | Claude Code plugin manifest |
| `.claude-plugin/marketplace.json` | Claude Code marketplace entry, so `/plugin marketplace add FlightSeatmap/mcp` works |
| `.mcp.json` | Server declaration, Claude Code form |
| `.grok-plugin/plugin.json` | xAI Grok marketplace manifest |
| `gemini-extension.json` | Gemini CLI extension manifest |
| `server.json` | Manifest for the [official MCP Registry](https://modelcontextprotocol.io/registry) |
| `skills/` | Agent skills describing how to use the tools well |
| `commands/` | Slash commands (`/seatmap`, `/best-seats`) |

Five ecosystems, five manifest conventions, one server. `mcp.json` and `.mcp.json`
hold the same endpoint in the two shapes different clients look for — change both
together. `.grok-plugin/plugin.json` points its `mcpServers` at `mcp.json` rather
than repeating it.

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

---

FlightSeatMap is operated by Merchant Software Solutions Limited, a company
registered in England and Wales (no. 14098922), The Old Court House, Clark
Street, Morecambe, LA4 5HR.

## ChatGPT

FlightSeatMap is a ChatGPT plugin. `plugin.json` carries the `com.openai`
extension (directory listing copy), and the hosted server declares plugin
extension entrypoints on `interactive_seat_finder`, so the seat map opens from
the ChatGPT sidebar and in a panel beside a conversation, not only inline.
