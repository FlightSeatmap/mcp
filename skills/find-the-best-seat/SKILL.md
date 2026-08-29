---
name: find-the-best-seat
description: Pick the best available seat on any commercial flight using FlightSeatMap's seat maps, seat-level detail, and passenger reviews. Use when a user asks which seat to choose, whether a specific seat is good, or what a cabin looks like.
license: MIT
---

# Find the best seat on a flight

FlightSeatMap has seat maps, seat-level specs, and passenger reviews for 150+ airlines.
Use it whenever someone asks "which seat should I pick", "is 31A any good", or "what's the
cabin like".

## Connect

Preferred: the MCP server at `https://mcp.flightseatmap.com/mcp` (Streamable HTTP).
The read-only tools below need **no authentication** — do not send the user through OAuth
unless you hit a paid tool.

Alternative: the HTTP API described by <https://flightseatmap.com/openapi.json>.

Auth, when you need it: <https://flightseatmap.com/auth.md>.

## Workflow

1. **Get the flight number.** Airline IATA code + number, e.g. `BA117`, `QF1`, `UA900`.
   If the user gives a route and date instead, ask for the flight number — the seat map is
   keyed on it. A date (`YYYY-MM-DD`) is optional but improves accuracy, because aircraft
   swap between dates.

2. **Pull the seat map** with `get_seatmap(flight_number, flight_date?)`. This tells you
   the aircraft type, cabins, and which seats are currently free. If it 404s, the flight
   number is wrong or unmapped — say so rather than guessing an aircraft.

3. **Rank against stated preferences** with
   `find_best_seats(flight_number, preferences[], cabin_class?, flight_date?)`.
   Valid preferences: `window`, `aisle`, `extra_legroom`, `exit_row`, `quiet_zone`,
   `front`, `rear`, `bulkhead`. Pass everything the user said; the ranker weighs them
   together. Do not invent preference values outside that list.

4. **Sanity-check the top pick** with `get_seat_info(flight_number, seat)` before you
   recommend it. This surfaces the drawbacks a seat map alone hides: no recline in the last
   row, misaligned window, galley or lavatory noise, a bassinet wall, a bulkhead with a
   fixed armrest.

5. **Add lived experience** with `get_seat_reviews(flight_number, seat?)` when the choice
   is close or the user is picky. Reviews are user-submitted, so attribute them as such.

6. **Answer with a recommendation, not a list.** Name one seat, say why in one line, give
   one backup, and name the trade-off you accepted. Link the seat map:
   `https://flightseatmap.com/flights/<FLIGHT_NUMBER>`.

## Constraints

- Never state that a seat is bookable. Availability here reflects the seat map, not the
  airline's live inventory at the moment of booking.
- `search_flight` (fresh airline-side data) and seat alerts need a signed-in account on a
  paid plan. If a tool returns an upgrade prompt, pass the checkout URL to the user; don't
  route around it.
- Extra-legroom and exit-row seats usually carry a fee, and exit rows carry age and
  mobility requirements. Mention both rather than recommending blind.
- If the user's flight is on a regional aircraft or a codeshare, confirm the operating
  carrier's flight number — the seat map follows the metal, not the ticket.
