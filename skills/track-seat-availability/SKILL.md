---
name: track-seat-availability
description: Watch a flight and email the user when a better seat frees up, using FlightSeatMap seat alerts. Use when a user is stuck in a bad seat, wants a window/aisle/exit row that is currently taken, or wants two seats together.
license: MIT
---

# Track seat availability on a flight

Seats free up constantly as other passengers rebook, upgrade, or check in. FlightSeatMap
alerts watch a flight and email the user the moment a matching seat opens.

Reach for this **after** `find-the-best-seat` has established that the seat the user wants
is currently taken.

## Connect

MCP server: `https://mcp.flightseatmap.com/mcp` (Streamable HTTP).

Alerts require a **signed-in account**, so unlike the read-only seat map tools these need
OAuth. Run the flow in <https://flightseatmap.com/auth.md> — dynamic client registration
plus PKCE, no API key to request. Some alert types also require a paid plan; the tool
response carries the checkout URL if so.

## Workflow

1. **Confirm the flight is in the database.** Call `get_seatmap(flight_number)` first.
   If it misses, call `search_flight` (paid) to pull it in. `create_seat_alert` fails on
   unknown flights.

2. **Create the alert** with `create_seat_alert`:

   - `flight_number` — e.g. `QF1`
   - `flight_date` — `YYYY-MM-DD`, today through +60 days. Outside that window it fails.
   - `cabin_class` — `economy`, `premium_economy`, `business`, `first`
   - `seat_preference` — one of `window`, `aisle`, `bulkhead`, `exit_row`,
     `extra_legroom`, `quiet_zone`, `front_of_cabin`, `rear_of_cabin`, `specific`,
     `adjacent_seats`, `minimum_seats`, `class_availability`, `better_seat_upgrade`
   - `specific_seat` — required when `seat_preference` is `specific` (e.g. `12A`)
   - `adjacent_seats_count` — required for `adjacent_seats` / `minimum_seats`, 2–9

   Pick the preference that matches what the user actually said. "Two seats together" is
   `adjacent_seats` with a count, not two separate `window` alerts. "Anything better than
   what I have" is `better_seat_upgrade`.

3. **Confirm back to the user** what you're watching, on which date, and that the alert
   arrives by email — not through you.

4. **Manage existing alerts** with `list_seat_alerts` (shows active and inactive, with IDs)
   and `delete_seat_alert(id)`. Always list before deleting; never guess an ID.

## Constraints

- One alert per preference per flight. Don't blanket a flight with alerts — list first and
  reuse.
- Alerts stop at departure and can't be created for past dates or beyond 60 days out.
- An alert does not hold or book a seat. The user still has to go to the airline and take
  it, and it may be gone by the time they do. Say this once, plainly.
- Fee-carrying seats (exit row, extra legroom) will still charge on selection. An alert
  doesn't waive that.
- Alert creation is a write. Confirm with the user before creating, and never create one as
  a side effect of a question about seats.
