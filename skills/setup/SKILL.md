---
name: setup
description: Set up FlightSeatmap and pick a seat on the user's next flight. Use when the user installs the FlightSeatmap plugin, runs /flightseatmap:setup, or asks to get started with FlightSeatmap.
license: MIT
---

# Set up FlightSeatmap

Goal: the user ends this session with one recommended seat on a real flight, and knows what else to ask.
Keep it to a few messages. Do not explain the plugin before you start.

## 1. Check the connection

The plugin loads the `flightseatmap` MCP server on its own. If no `flightseatmap` tools are available,
run this, then tell the user to restart Claude Code and run `/flightseatmap:setup` again:

```bash
claude mcp add --transport http flightseatmap https://mcp.flightseatmap.com/mcp
```

Every tool needs a FlightSeatmap sign-in. The first tool call opens a browser window for OAuth. Tell the user to expect it and to use a free account if they have none.

## 2. Ask once

Ask all of this in one message:

- Flight number, such as `BA117` or `QF1`. If they give a route and date instead, ask for the flight number: seat maps are keyed on it.
- Date (`YYYY-MM-DD`), if they know it.
- What matters most: window, aisle, extra legroom, exit row, quiet, or sitting with someone.

If they have no trip, offer `QF1` as a demo and say it is a demo.

## 3. Show a result

1. `get_seatmap(flight_number, flight_date?)`. If it 404s, say the flight is unmapped. Do not guess an aircraft.
2. `find_best_seats(flight_number, preferences[], flight_date?)`. Use only these preferences: `window`, `aisle`, `extra_legroom`, `exit_row`, `quiet_zone`, `front`, `rear`, `bulkhead`.
3. `get_seat_info(flight_number, seat)` on the top pick before recommending it.

Recommend one seat with one line of why, one backup, and the trade-off. Link
`https://flightseatmap.com/flights/<FLIGHT_NUMBER>`.

Availability reflects the seat map, not the airline's live inventory. Say so once.

## 4. Offer an alert

If the best seats are taken or the user wants a better one, offer a seat alert: FlightSeatmap emails them when a matching seat frees up.

Alerts need a paid plan. Call `create_seat_alert`. If the tool returns an upgrade prompt, give the user the checkout URL it returns. Do not work around it.

## 5. Finish

Tell the user, in two lines, what they can ask next:

- "Which seat should I pick on <flight>?" or "Compare 31A and 31K on <flight>"
- "Find two seats together on <flight>" or "Alert me if an exit row opens up"

Also mention `/flightseatmap:seatmap <flight>` for a quick lookup.
