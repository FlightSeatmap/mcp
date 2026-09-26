---
name: compare-seats
description: Compare two to four specific seats on a flight and pick a winner using FlightSeatMap seat details and passenger reviews. Use when a user asks "12A or 31K?", "is this seat worse than that one", or wants to check a seat they were assigned.
license: Proprietary — see https://flightseatmap.com/terms
---

# Compare specific seats

## Connect

MCP server: `https://mcp.flightseatmap.com/mcp` (Streamable HTTP). Setup and more skills:
<https://flightseatmap.com/.well-known/agent-skills/index.json>. Auth: <https://flightseatmap.com/auth.md>.

These tools need **no authentication**.

## Workflow

1. **Collect the flight number and the seats.** Flight number = airline IATA code + number
   (`BA117`). Seats = row + letter (`12A`). Ask for the date (`YYYY-MM-DD`) if the user
   knows it — aircraft swap between dates.

2. **Call `get_seat_info(flight_number, seat_number, flight_date?)` once per seat.** Note
   cabin, window/aisle/middle, legroom, recline, exit row, bulkhead, and proximity to
   galleys and lavatories.

3. **Call `get_seat_reviews(flight_number, seat_number)` for each seat.** Reviews are
   user-submitted — attribute them as such. No reviews is normal; do not treat it as bad.

4. **Flag hidden drawbacks:** no recline (row in front of an exit, last row), missing or
   misaligned window, fixed armrests with tray tables (narrower seat), bassinet positions
   (infant noise), lavatory queues, reduced under-seat storage at bulkheads and exit rows.

5. **Answer with a verdict.** One line per seat with its main strength and main drawback,
   then name the winner and the one reason it wins. If the user's priority (sleep, work,
   quick exit, legroom) changes the winner, say so.

## Constraints

- If a seat does not exist on the aircraft, say so. Do not guess a nearby seat.
- Do not state that a seat is bookable. Availability comes from the cached seat map.
- Link the map: `https://flightseatmap.com/flights/<FLIGHT_NUMBER>`.
