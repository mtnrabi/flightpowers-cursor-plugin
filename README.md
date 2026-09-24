# FlightPowers: live flight and hotel prices in Cursor

Two remote MCP servers that read live prices, not schedules. Sign in with Google and your first
10 searches each day are free and ad-free on our key, with nothing to paste. Past that you bring
your own RapidAPI key and the calls are billed to your plan, not ours.

## What you get

**Flights** — `search_oneway_flights`, `search_roundtrip_flights`

- A round trip is ONE request. Both legs come back priced together, one total, one booking
  link. You don't search twice and add the numbers up.
- Every fare carries Google's own historical range for that route and period
  (`price_insights_low`, `price_insights_high`) and a low / typical / high verdict, so the
  agent can say whether the number is actually a good one.
- One call takes a date range and a list of destination airports and expands them internally.
  "Cheapest flight to anywhere in October" is one call, not thirty. `search_coverage` says
  which dates and destinations the answer is actually based on.

**Hotels** — `search_hotels`, `find_hotel_by_name`, `compare_hotel_rates`

- Live Booking.com room rates for a destination the way a person says it ("Rome", "Tokyo
  Shibuya"), or for one property you name.
- `price_as_seen_from` prices the same stay through a residential connection in a country you
  pick, which is how you check rate parity and geo-pricing.
- `compare_hotel_rates` prices the same stay on more than one source and returns a row per
  source: cheapest total, median total, how many places were priced, and when the rows were
  read. A source you hold no key for is listed in `providers_skipped`, not silently dropped.
- A check-in range plus a `nights` value prices every stay it expands to in one call.

Every tool is read-only and annotated as such (`readOnlyHint: true`, `destructiveHint: false`).

## Setup

**To just try it, skip step 1.** Install the plugin, leave `RAPIDAPI_KEY` unset, and sign in with
Google when Cursor offers it: the first 10 searches each UTC day run free and ad-free on our key.
A search is one date x destination combination, so one call over a three-day range spends three.
The allowance renews at 00:00 UTC; past it the tools answer with `search_status: "trial_exhausted"`
rather than an error. `compare_hotel_rates` and multi-source hotel searches are not part of it and
ask for your own key.

1. Get a RapidAPI key:
   [google-flights-live-api](https://rapidapi.com/mtnrabi/api/google-flights-live-api) for
   flights and [booking-live-api](https://rapidapi.com/mtnrabi/api/booking-live-api) for
   hotels. BASIC is $0 with 10 requests a month, no card and no approval step. PRO is $10 a
   month.
2. Install this plugin and set the `RAPIDAPI_KEY` variable to your key. The plugin ships only
   the placeholder; the value stays with you.
3. Ask Cursor for a fare.

The servers also accept OAuth 2.1 sign-in (dynamic client registration + PKCE) if you would
rather not paste a key into a config file.

## What the plugin contains

```
.cursor-plugin/plugin.json   manifest, declares the RAPIDAPI_KEY variable
mcp.json                     the two remote servers, streamable HTTP
assets/logo.png
```

Server URLs:

- flights — `https://flights.flightpowers.com/mcp`
- hotels — `https://hotels.flightpowers.com/mcp`

## Billing

RapidAPI meters and invoices. We never see your card. One search is one request; a call with a
date range and a destination list spends one request per date x destination, and every response
reports what it spent and what is left in `api_usage`.

## Links

Docs https://flightpowers.com/docs ·
Privacy https://flights.flightpowers.com/privacy ·
Terms https://flights.flightpowers.com/terms ·
Contact matan@flightpowers.com

MIT licensed. The plugin is open source; the servers are hosted.
