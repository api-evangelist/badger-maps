# Badger Maps (badger-maps)

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
