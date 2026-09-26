---
name: extra-legroom-seats
description: Find the seats with the most legroom on a flight — exit rows, bulkheads, and extra-legroom economy — and explain the trade-offs of each. Use when a user is tall, asks for more legroom, or asks about exit rows or bulkhead seats.
license: Proprietary — see https://flightseatmap.com/terms
---

# Find extra legroom

## Connect

MCP server: `https://mcp.flightseatmap.com/mcp` (Streamable HTTP). Setup and more skills:
<https://flightseatmap.com/.well-known/agent-skills/index.json>. Auth: <https://flightseatmap.com/auth.md>.

These tools need **no authentication**, except the optional alert.

## Workflow

1. **Collect the flight number, the date, and the cabin.**

2. **Call `find_best_seats(flight_number, ["extra_legroom", "exit_row", "bulkhead"], cabin_class?, flight_date?)`.**
   Add `window` or `aisle` if the user wants one.

3. **Call `get_seat_info` on the top 2–3 results** and sort them into:
   - **Exit row:** most legroom. Often no recline in the row in front of a second exit.
     Tray table and screen in the armrest, so the seat is narrower. No bag under the
     seat in front during take-off and landing.
   - **Bulkhead:** no one reclines into you, but a wall limits foot space and there is
     no under-seat storage. Bassinet positions mean possible infant noise.
   - **Extra-legroom economy:** normal seat with more pitch. Fewest drawbacks.

4. **Recommend one seat** and name its drawback. Tall travellers usually want an exit
   row aisle seat, so they can stretch one leg into the aisle.

5. **If none is free,** offer `create_seat_alert` with `seat_preference` set to
   `extra_legroom`, `exit_row`, or `bulkhead` (needs a signed-in paid account).

## Constraints

- Exit-row passengers must be 15+ (varies by airline), able and willing to help in an
  evacuation, and usually cannot travel with an infant or a pet. Always say this.
- These seats usually cost extra. Do not quote a price unless `get_seat_info` returns one.
