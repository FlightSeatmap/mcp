---
description: Find the best seats on a flight matching your preferences
argument-hint: "[flight_number] [preferences...]"
allowed-tools:
  - mcp__flightseatmap__get_seatmap
  - mcp__flightseatmap__find_best_seats
  - mcp__flightseatmap__get_seat_info
---

# Find Best Seats

The user wants to find the best available seats on a flight.

## Arguments

The user invoked this command with: $ARGUMENTS

Parse the arguments to extract:
- **Flight number**: Airline code + number (e.g. QF1, BA178)
- **Preferences**: Any of: window, aisle, extra_legroom, exit_row, quiet_zone, front, rear, bulkhead
- **Cabin class**: Optional: economy, premium_economy, business, first

If no preferences given, ask what matters to them.

## Process

1. Use `find_best_seats` with the flight number and parsed preferences.
2. Present the top 5 seats in a table: Seat | Cabin | Features | Match Score | Price
3. Offer to show details on any seat with `get_seat_info`.
4. Include the interactive seatmap link.

## Examples

```
/fsm:best-seats QF1 window extra_legroom
/fsm:best-seats BA178 aisle front business
/fsm:best-seats AA716 exit_row quiet_zone economy
```
