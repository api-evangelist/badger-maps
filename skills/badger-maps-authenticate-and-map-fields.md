---
name: Authenticate to Badger Maps and resolve custom field mappings
description: Exchange credentials for a Badger Maps API token, then fetch the profile that tells you what every custom_text/custom_numeric field on an account actually means for this team. Do this once before any other Badger skill.
api: openapi/badger-maps-users-api-openapi.yml
operations:
  - login
  - getProfile
  - searchUsers
---

# Authenticate to Badger Maps and resolve custom field mappings

Badger Maps custom fields are per-team. The same account property `custom_text2`
means "Next Step" at one company and something else at the next. **You cannot
correctly read or write a Badger account without first fetching the profile
map.** Run this skill first and cache the result for the session.

Base URL: `https://badgerapis.badgermapping.com/api/2`

## Prerequisite the docs make easy to miss

API/Developer Key access is included with paid plans but is **not self-serve**.
The key must be enabled by Badger Maps support (`support@badgermapping.com`)
before it authenticates. A well-formed key on a team that has not had it enabled
returns `401` exactly like a bad key does. If you are getting a persistent `401`
with a key the user believes is valid, that is the likely cause — say so rather
than retrying.

## Step 1 — get a token (`login`)

`POST /login/` with an `application/x-www-form-urlencoded` body:

```
username=<user>&password=<password>
```

Send no `Authorization` header on this call.

**Read the body, not the status line.** This endpoint answers `200` on failure
too, with the outcome in a `status` string:

| `status` | What to do |
|---|---|
| `login_successful` | Store `token` and `user_id`. `user_id` is needed as `account_owner` on account creation. |
| `error_missing_key` | A credential field was absent. Fix the body. |
| `error_account_does_not_exist` | Wrong username **or** wrong password — Badger deliberately does not distinguish. Do not report "user not found". |
| `error_account_disabled` | Not retryable. Escalate to `support@badgermapping.com`. |
| `error_trial_expired` | Not retryable. The team needs a paid plan. |
| `error_account_delinquent` | Not retryable. Billing issue. |
| `error_non_post_request` | You used the wrong method. Use POST. |

Never retry a non-retryable status in a loop.

## Step 2 — send the token on every later call

```
Authorization: Token <token>
```

The scheme word is `Token`, **not** `Bearer`. A `Bearer` prefix will not
authenticate.

## Step 3 — fetch the field map (`getProfile`)

`GET /profiles/` returns the authenticated user's profile. The part that matters:

- `datafields[]` — each entry has `name` (a short code like `ct`, `ct2`, `cn3`),
  `label` (the human name this team uses), `type` (`text` or `numeric`),
  `filterable`, and for text fields a `values[]` pick list.
- `id` — the user id.
- `is_manager` — whether this token may act on other users' accounts.
- `crm_type` / `crm_base_url` — which CRM this team is bound to.
- `apptlog_fields[]` / `acctlog_fields[]` — custom fields on check-ins and account logs.

**Build the mapping table:**

| `datafields[].name` | Account field to read/write |
|---|---|
| `ct` | `custom_text` |
| `ct2` | `custom_text2` |
| `ct12` | `custom_text12` |
| `cn` | `custom_numeric` |
| `cn3` | `custom_numeric3` |

The range runs to `custom_text30` / `custom_numeric30`. Resolve by `label` when
the user says "set Next Step to X" — find the datafield whose `label` is
`Next Step`, take its `name`, convert to the account field.

When a text datafield has a non-empty `values[]` and
`is_user_can_add_new_text_values` is `false`, only those values are accepted.
Do not invent a new one.

## Step 4 — resolve a user id from an email (`searchUsers`)

`GET /search/users/?q=<email or badger user id>`

Returns `{username, first_name, id, email}`. Use this when you have a rep's email
address and need their numeric `id` as the `account_owner` value on
`createAccount`.

## Rules

- Cache the profile for the session; do not call `GET /profiles/` before every write.
- Everything counts against 25,000 requests per day per team, shared across all
  users. There are no rate-limit response headers, so you cannot see how much is
  left — budget conservatively.
- Do not log the token or the password.
