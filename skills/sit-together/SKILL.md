---
name: sit-together
description: Find adjacent seats so a couple, family, or group can sit together on a flight, and alert them when a block of seats opens up. Use when a user travels with others and asks where they can sit together.
license: Proprietary — see https://flightseatmap.com/terms
---

# Seat a group together

## Connect

MCP server: `https://mcp.flightseatmap.com/mcp` (Streamable HTTP). Setup and more skills:
<https://flightseatmap.com/.well-known/agent-skills/index.json>. Auth: <https://flightseatmap.com/auth.md>.

Reading the seat map needs **no authentication**. Alerts need a signed-in paid account.

## Workflow

1. **Collect the flight number, the date, the group size, and the cabin.** Ask if the
   group includes an infant or a small child — that changes the answer.

2. **Call `get_seatmap(flight_number, flight_date?)`.** Read the cabin layout (for
   example 3-3, 2-4-2, 3-4-3) and the free seats.

3. **Find blocks of free seats in the same row.** No aisle between seats is best. Two
   seats across an aisle in the same row is second best. Two rows directly behind each
   other is third best. Use the layout:
   - 2 people: an A-B or J-K pair on a 2-seat side beats a window + middle pair.
   - 3 people: one full side of a 3-3 or 3-4-3 row.
   - 4 people: the middle block of a 3-4-3 or 2-4-2 row, or 2 + 2 across the aisle.
   - 5+: split into sub-groups in consecutive rows. Seat each child next to an adult.

4. **Use `find_best_seats(flight_number, preferences[], cabin_class?)`** when the user
   adds preferences (`window`, `aisle`, `front`, `rear`, `quiet_zone`,
   `bulkhead`). Bulkhead rows often hold bassinets for infants.

5. **If no block is free, offer an alert.** Call `create_seat_alert` with
   `seat_preference: "adjacent_seats"` and `adjacent_seats_count` = group size (2–9),
   plus `flight_number`, `flight_date` (within 60 days), and `cabin_class`
   (`economy`, `premium_economy`, `business`, `first`).

## Constraints

- Children cannot sit in exit rows. Infants on laps usually need a seat with an extra
  oxygen mask — tell the user to confirm with the airline.
- Airlines often hold back seats until check-in. A map with no free block today can change.
- If an alert tool returns a sign-in or upgrade prompt, pass the URL to the user.
