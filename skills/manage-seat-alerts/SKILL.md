---
name: manage-seat-alerts
description: Review, clean up, and replace a user's FlightSeatMap seat alerts. Use when a user asks what alerts they have, wants to stop an alert, or wants to change what an alert watches for.
license: Proprietary — see https://flightseatmap.com/terms
---

# Manage seat alerts

## Connect

MCP server: `https://mcp.flightseatmap.com/mcp` (Streamable HTTP). Setup and more skills:
<https://flightseatmap.com/.well-known/agent-skills/index.json>. Auth: <https://flightseatmap.com/auth.md>.

All tools here need a **signed-in account**. Connecting the MCP server starts OAuth.

## Workflow

1. **Call `list_seat_alerts()`.** Show each alert as one line: flight, date, what it
   watches for, and active or inactive. Point out alerts for flights that already left.

2. **To stop an alert,** confirm which one with the user, then call
   `delete_seat_alert(alert_id)`. Never delete alerts in bulk without confirmation.

3. **To change an alert,** there is no edit tool. Create the new alert with
   `create_seat_alert` first, then delete the old one. That way the user never has a gap
   in coverage. Valid `seat_preference` values: `window`, `aisle`, `bulkhead`,
   `exit_row`, `extra_legroom`, `quiet_zone`, `front_of_cabin`, `rear_of_cabin`,
   `specific` (needs `specific_seat`), `adjacent_seats` / `minimum_seats` (need
   `adjacent_seats_count` 2–9), `class_availability`, `better_seat_upgrade`.

4. **If `create_seat_alert` says the flight is not in the database,** call
   `search_flight(flight_number, flight_date)` first, then retry. `search_flight` uses
   a search credit — tell the user before you call it.

## Constraints

- Alerts only accept flights from today to 60 days out.
- If a tool returns a sign-in or upgrade prompt, pass the URL to the user. Do not route
  around it.
- Alerts are sent by email to the account address. Do not promise SMS or push.
