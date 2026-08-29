---
description: Look up the seat map for a flight
argument-hint: "[flight_number] [date]"
allowed-tools:
  - mcp__flightseatmap__get_seatmap
  - mcp__flightseatmap__get_seat_info
  - mcp__flightseatmap__find_best_seats
  - mcp__flightseatmap__search_flight
---

# Seat Map Lookup

The user wants to look up a flight seat map.

## Arguments

The user invoked this command with: $ARGUMENTS

Parse the arguments to extract:
- **Flight number**: Airline code + number (e.g. QF1, BA178, AA716, EK1)
- **Date**: Optional flight date (YYYY-MM-DD)

## Process

1. Use `get_seatmap` with the flight number (and date if provided).
2. Summarize the results: aircraft type, route, total/available seats per cabin.
3. Ask if they want to find the best seats for their preferences, or check a specific seat.
4. If they have preferences, use `find_best_seats`.
5. Always include the link to the interactive seatmap.

## Examples

```
/fsm:seatmap QF1
/fsm:seatmap BA178 2026-06-15
/fsm:seatmap AA716
```
