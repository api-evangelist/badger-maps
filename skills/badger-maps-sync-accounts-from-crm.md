---
name: Sync accounts into Badger Maps from a CRM or list
description: Create and update Badger Maps accounts (customers) from an external source, assigning the right owning rep and writing team-specific custom fields correctly. Handles the no-idempotency problem explicitly.
api: openapi/badger-maps-accounts-api-openapi.yml
operations:
  - searchUsers
  - getProfile
  - listAccounts
  - createAccount
  - getAccount
  - updateAccount
  - deleteAccount
---

# Sync accounts into Badger Maps from a CRM or list

Run `badger-maps-authenticate-and-map-fields` first. You need a token, the
`datafields[]` map, and the numeric user id of the owning rep.

Base URL: `https://badgerapis.badgermapping.com/api/2`
All write bodies are `application/x-www-form-urlencoded`. Values must be URL encoded.

## The rule that matters most: there is no idempotency key

Badger Maps has **no** `Idempotency-Key` header and no request-replay contract.
A retried `POST /customers/` creates a second account. Before you retry a create
whose response you did not see, or before you create anything in a re-run:

1. Call `listAccounts` (`GET /customers/`) — it returns
   `[{first_name, last_name, id}, …]` for every account the token can see.
2. Match on `last_name` (Badger's list projection carries only names and ids).
3. If a match exists, `updateAccount` instead of creating.

Keep your own external-id → Badger-id ledger across runs. `Account.customer_id`
and `Account.crm_id` are nullable passthrough fields you can write your external
identifier into — use them so the next run can reconcile.

## Step 1 — resolve the owner (`searchUsers`)

`GET /search/users/?q=<rep email>` → `id`. That integer is the `account_owner`
value. If you skip this, the create fails: `account_owner` is required.

Reassignment note: `updateAccount` accepts `account_owner` to move an account to
another rep, **but only if the token belongs to that user's manager**. Check
`is_manager` on the profile before attempting it.

## Step 2 — create (`createAccount`)

`POST /customers/`

Required: `last_name`, `address`, `account_owner`.
Also required as *keys*, though the values may be empty: `phone_number`, `email`.

```
last_name=Roger Smith&address=3458 Mackinaw Street, Saginaw, MI&account_owner=441&email=&phone_number=
```

**Skip geocoding when you already have coordinates.** Pass `lat` and `lng`
alongside `address` (the address is still required) and Badger will use your
point instead of geocoding the string:

```
last_name=Roger Smith&address=1 Union St, San Francisco, CA&lat=43.435175&lng=-83.9912469&account_owner=441&email=&phone_number=
```

This is the difference between a pin on the right building and a pin on the
wrong side of town for messy addresses.

**Custom fields.** Use the mapping from the profile. A datafield named `ct2`
("Next Step") is written as `custom_text2`; `cn3` as `custom_numeric3`; bare
`ct`/`cn` as `custom_text`/`custom_numeric`.

Response is `201` with `{customer, profile, location}` — an account object, the
refreshed profile, and the geocoded location that was created for it. Record
`customer.id` and `location.id`; the location id is what the Locations API needs
later and it is **not** the account id.

## Step 3 — update (`updateAccount`)

`PATCH /customers/{account_id}/` with only the fields you are changing:

```
custom_text=Urgent
```

Returns the full updated account including its `locations[]` array.

## Step 4 — read back (`getAccount`)

`GET /customers/{account_id}/` returns the full account: names, phone, email,
`notes`, `original_address`, `crm_id`, `customer_id`, all thirty
`custom_text*`/`custom_numeric*` slots, and the embedded `locations[]`.

The address on an account is **not** editable through this endpoint — see
`badger-maps-correct-account-location`.

## Step 5 — delete (`deleteAccount`)

`DELETE /customers/{account_id}/` returns `204` and no body. There is no
soft-delete, no undo, and no restore endpoint. Confirm with the user before
calling it, and never delete as a cleanup step in a failed sync.

## Errors

- `400` — a required field is missing or malformed on create.
- `401` — token missing, malformed, or not yet enabled by Badger support.
- `404` — that account id does not exist or is not visible to this token.

There is no `problem+json` body and no machine error code. Report the status code
and the operation you attempted.

## Budget

25,000 requests per day per **team**, shared. A full-catalog `listAccounts`
sweep plus per-account `getAccount` calls burns that fast on a large territory.
Prefer one `listAccounts` and your own ledger over per-record lookups.
