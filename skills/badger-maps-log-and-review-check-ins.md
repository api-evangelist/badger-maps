---
name: Log and review Badger Maps check-ins
description: Record a field visit against an account and read back the visit history. Covers the /appointments/ naming trap and the fact that check-ins cannot be edited or deleted over the API.
api: openapi/badger-maps-check-ins-api-openapi.yml
operations:
  - listCheckIns
  - createCheckIn
  - getProfile
  - listAccounts
---

# Log and review Badger Maps check-ins

A check-in is a timestamped record that a rep visited an account. Run
`badger-maps-authenticate-and-map-fields` first.

Base URL: `https://badgerapis.badgermapping.com/api/2`

## The naming trap

Check-ins are served under **`/appointments/`**, not `/check-ins/`. The field on
the check-in object that points at the account is called **`customer`**, and the
query parameter on the read is called **`customer_id`**. Both refer to a Badger
account id.

## Step 1 — read the history (`listCheckIns`)

`GET /appointments/?customer_id={account_id}`

Returns a JSON array:

```json
[
  {
    "id": 57250,
    "crm_id": null,
    "customer": 1981937,
    "log_datetime": "2014-01-23T10:08:44.133",
    "type": "Drop-in",
    "comments": "Stopped by office, left business cards",
    "extra_fields": null,
    "created_by": "John Doe"
  }
]
```

`customer_id` is required — there is no "all recent check-ins" endpoint, and no
date-range filter. To build an activity report across a territory you must call
this once per account, which is expensive against the 25,000/day team quota.
Get the account id list from `listAccounts` once and iterate deliberately.

There is no pagination. The full history for an account comes back in one array.

## Step 2 — record a visit (`createCheckIn`)

`POST /appointments/` with an `application/x-www-form-urlencoded` body:

```
customer=1981937&comments=Met with client, follow up next week&type=Drop-in
```

- `customer` — the account id (note: not `customer_id` on the write).
- `type` — the visit type. `Drop-in` is the value used throughout Badger's own
  documentation. Types are configurable per team; read the allowed values from
  the team's own data rather than assuming.
- `comments` — free text.

`log_datetime` and `created_by` are set server-side from the token. You cannot
backdate a check-in through this endpoint.

Response is `201` with the new check-in object. Record its `id`.

## What you cannot do

- **No update.** There is no `PATCH /appointments/{id}/`. A typo in `comments`
  cannot be corrected over the API.
- **No delete.** There is no `DELETE /appointments/{id}/`, even though the
  profile carries an `is_user_can_delete_checkins` flag for the application UI.
- **No idempotency key.** A retried POST logs a second visit. Before retrying a
  create whose response you did not see, call `listCheckIns` for that account and
  look for a matching `comments`/`type` at the top of the array.

Because a check-in cannot be edited or removed, treat every `createCheckIn` as
irreversible. Confirm the account id and the comment text with the user before
writing.

## Custom fields on check-ins

`getProfile` returns `apptlog_fields[]` — the team's custom fields for
appointment logs, with `name`, `label`, `type` and, for pick lists, `options[]`.
The check-in object exposes `extra_fields` for these. Resolve the mapping from
the profile before writing anything into it.

## Webhook

Badger emits a **Check-in Created** webhook. Subscription is not self-serve —
the team must email an Integration Specialist at `support@badgermapping.com`.
Payload shape and signing are not published, so do not build verification logic
speculatively. See `asyncapi/badger-maps-webhooks.yml`.

## Errors

- `400` — malformed body or missing `customer`.
- `401` — token missing, malformed, or not enabled by Badger support.
