---
name: Read Badger Maps routes and correct account locations
description: Pull a rep's optimized route with its ordered waypoints, walk each stop back to its account, and fix a mis-geocoded address through the Locations API. Routes are read-only over the API.
api: openapi/badger-maps-routes-api-openapi.yml
operations:
  - listRoutes
  - getRoute
  - getAccount
  - updateLocation
---

# Read Badger Maps routes and correct account locations

Run `badger-maps-authenticate-and-map-fields` first.

Base URL: `https://badgerapis.badgermapping.com/api/2`

## Routes are read-only

There is no create, update, delete or re-optimize operation for routes or
waypoints in the Badger Maps API. You can read a route that a rep built in the
application; you cannot build one, reorder it, or add a stop. If the user asks
you to plan a route, say plainly that this must be done in the Badger Maps app
or mobile client — do not simulate it.

## Step 1 — list routes (`listRoutes`)

`GET /routes/` returns the authenticated user's routes as a thin projection:

```json
[
  {"id": 1235, "name": "Day 3 of Trip", "route_date": "2019-09-19"},
  {"id": 1234, "name": "Monday Route", "route_date": "2019-09-12"}
]
```

Only `id`, `name` and `route_date`. There is no date filter and no pagination —
filter by `route_date` client-side. Routes belong to the authenticated user;
there is no documented parameter for reading another rep's routes the way
`listAccounts` accepts `rn`.

## Step 2 — get the stops (`getRoute`)

`GET /routes/{route_id}/` returns the full route:

- `name`, `route_date`, `start_time`, `duration`, `start_address`, `destination_address`
- `waypoints[]` — the ordered stops

Each waypoint carries:

| Field | Meaning |
|---|---|
| `position` | Sort key. **Order the array by `position`** — do not trust array order. |
| `name`, `address` | Denormalized display strings for the stop. |
| `lat`, `long`, `location` | Coordinates; `location` is a WKT `POINT` string. |
| `layover_minutes` | Planned time at the stop. |
| `appt_time` | Scheduled time, or `null` for an unscheduled drop-in. |
| `customer_id` | The **account** id behind this stop. |
| `location_id` | The **location** id behind this stop. |

`customer_id` and `location_id` are different integers pointing at different
resources. Confusing them is the single most common error against this API.

## Step 3 — walk a stop back to its account (`getAccount`)

`GET /customers/{customer_id}/` using the waypoint's `customer_id` gives you the
full account — contact details, notes, `crm_id`, and every
`custom_text*`/`custom_numeric*` value. Combine with the `datafields[]` map from
the profile to render "Next Step: drop off a sample" style briefing text for
each stop on the day's route.

## Step 4 — correct a bad address (`updateLocation`)

When a stop is pinned in the wrong place, the fix goes through the **Locations**
API, not the Accounts API. The account's address is not editable through
`updateAccount`.

`PATCH /locations/{location_id}/` with an `application/x-www-form-urlencoded` body:

```
address=5731 Palmer Way, Carlsbad, CA&lat=33.13773&lng=-117.272667
```

- `address` is required.
- `lat` and `lng` are optional. **Supply them when you have a trusted point** —
  Badger will use your coordinates and skip geocoding the address string, which
  is how you fix a stop that geocoding put on the wrong block.
- Note the asymmetry: the request parameter is `lng`, the response field is `long`.

Returns the updated location:

```json
{"id": 1611109, "city": "Carlsbad", "name": null, "zipcode": "92010",
 "long": -117.272667, "state": "CA", "lat": 33.13773,
 "address_line_1": "5731 Palmer Way", "location": "POINT (-117.272667 33.13773)"}
```

Get `location_id` from the waypoint's `location_id`, or from the `locations[]`
array embedded on the account — **never** from the account id.

## What this does not do

- Changing a location does **not** re-optimize or reorder any route that already
  contains that stop.
- There is no operation to create or delete a location on its own; locations are
  created as a side effect of `createAccount`.
- There is no idempotency key, but `updateLocation` is a `PATCH` to a known id
  and is naturally safe to repeat with the same body.

## Errors

- `401` — token missing, malformed, or not enabled by Badger support.
- `404` — that route or location id does not exist or is not visible to this token.
  A `404` on `updateLocation` very often means an account id was passed where a
  location id was required.
