# Badger Maps (badger-maps)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Badger Maps is field sales route planning, mapping, and CRM software for outside sales and field teams - it optimizes daily driving routes, maps and filters accounts on a territory, captures check-ins, and reports on rep activity. Badger Maps also exposes a token-authenticated REST API (base `https://badgerapis.badgermapping.com/api/2`) that lets teams programmatically manage accounts (customers), account locations, routes, check-ins, and users, and sync data with CRMs and other systems. API/Developer Key access is included with paid plans (max 25k requests per day, per team); the key must be enabled by contacting Badger Maps support.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/badger-maps/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/badger-maps/refs/heads/main/apis.yml)

## Tags

- Field Sales
- Route Planning
- Mapping
- CRM
- Sales Enablement
- Territory Management

## Timestamps

- **Created:** 2026-07-04
- **Modified:** 2026-07-04

## APIs

### Badger Maps Accounts API

List, create, retrieve, update, and delete accounts (called customers in the API) - the businesses and contacts a rep maps and visits. Accounts carry name, address, contact details, geocoded location(s), an account owner, and custom data fields, and are the primary object synced with CRMs.

- **Human URL:** [https://badgerupdatedapi.docs.apiary.io/](https://badgerupdatedapi.docs.apiary.io/)
- **Base URL:** `https://badgerapis.badgermapping.com/api/2`

#### Tags

- Accounts
- Customers
- CRM

#### Properties

- [Documentation](https://badgerupdatedapi.docs.apiary.io/)
- [API Reference](https://badgerupdatedapi.docs.apiary.io/#reference/accounts)
- [OpenAPI](openapi/badger-maps-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/badger-maps.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/badger-maps.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Badger Maps Locations API

Update the physical locations attached to an account - an account can have one or more addresses that Badger geocodes to latitude/longitude for mapping and routing. Correcting an address or supplying explicit coordinates re-pins the account on the map.

- **Human URL:** [https://badgerupdatedapi.docs.apiary.io/](https://badgerupdatedapi.docs.apiary.io/)
- **Base URL:** `https://badgerapis.badgermapping.com/api/2`

#### Tags

- Locations
- Geocoding
- Addresses

#### Properties

- [API Reference](https://badgerupdatedapi.docs.apiary.io/#reference/locations)
- [OpenAPI](openapi/badger-maps-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/badger-maps.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/badger-maps.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Badger Maps Check-Ins API

List and create check-ins (served under the `/appointments/` resource) - the timestamped logs a rep records against an account after a visit or call, carrying a type, free-text comments, the author, and a log datetime. Check-ins are the activity feed that powers reporting and CRM sync.

- **Human URL:** [https://badgerupdatedapi.docs.apiary.io/](https://badgerupdatedapi.docs.apiary.io/)
- **Base URL:** `https://badgerapis.badgermapping.com/api/2`

#### Tags

- Check-Ins
- Appointments
- Activity

#### Properties

- [API Reference](https://badgerupdatedapi.docs.apiary.io/#reference/check-ins)
- [OpenAPI](openapi/badger-maps-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/badger-maps.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/badger-maps.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Badger Maps Routes API

List a rep's optimized routes and retrieve a single route with its ordered waypoints - each waypoint references an account location, its position in the sequence, and timing details. Routes are the optimized driving plans Badger generates across a set of accounts.

- **Human URL:** [https://badgerupdatedapi.docs.apiary.io/](https://badgerupdatedapi.docs.apiary.io/)
- **Base URL:** `https://badgerapis.badgermapping.com/api/2`

#### Tags

- Routes
- Route Optimization
- Waypoints

#### Properties

- [API Reference](https://badgerupdatedapi.docs.apiary.io/#reference/routes)
- [OpenAPI](openapi/badger-maps-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/badger-maps.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/badger-maps.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Badger Maps Users API

Authenticate a user to obtain an API token, retrieve the authenticated user profile (company, settings, and the custom data-field schema), and search for managed users by email or ID. User IDs are required to own accounts and scope routes and check-ins across a managed team.

- **Human URL:** [https://badgerupdatedapi.docs.apiary.io/](https://badgerupdatedapi.docs.apiary.io/)
- **Base URL:** `https://badgerapis.badgermapping.com/api/2`

#### Tags

- Users
- Profiles
- Authentication

#### Properties

- [API Reference](https://badgerupdatedapi.docs.apiary.io/#reference/users)
- [OpenAPI](openapi/badger-maps-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/badger-maps.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/badger-maps.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Authentication

The API uses token authentication. Send an `Authorization: Token <api_key>` header on every request. The API/Developer Key must be enabled by Badger Maps support (support@badgermapping.com) and is included with paid Business and Enterprise seats, capped at 25,000 requests per day per team.

## Common Properties

- [GitHub Organization](https://github.com/BadgerMaps)
- [LinkedIn](https://www.linkedin.com/company/badger-maps)
- [Website](https://www.badgermapping.com)
- [Documentation](https://badgerupdatedapi.docs.apiary.io/)
- [Plans](plans/badger-maps-plans-pricing.yml)
- [Rate Limits](rate-limits/badger-maps-rate-limits.yml)
- [Fin Ops](finops/badger-maps-finops.yml)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
