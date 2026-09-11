# Backend walkthrough: restaurant discovery and menu reads

Status: implemented on backend branch `codex/vert-001-catalog`; owner mutations and ordering are planned, not implemented here.

## What this feature does

The customer can ask the backend for active restaurants near a latitude/longitude and then load one restaurant’s catalog. The database performs the serviceability distance filter using PostGIS. Menu prices are returned as integer INR minor units (`21900` means ₹219.00) so later checkout contracts do not depend on floating-point client arithmetic.

## Endpoints

```text
GET /api/v1/restaurants?latitude=24.1874&longitude=75.6396&radius_km=10&sort_by=distance&limit=20
GET /api/v1/restaurants/{restaurantId}
GET /api/v1/restaurants/{restaurantId}/menu
```

The list endpoint requires valid coordinates, bounds the radius to 25 km and the page size to 50, supports `rating` or `distance` sorting, and returns an opaque base64 cursor. The public endpoint is rate-limited by source IP. The current implementation does not yet add the Redis discovery cache described by the historical flowchart; that is a follow-up hardening task.

## Implementation path

1. `internal/services/restaurant/api/handler.go` validates query parameters and UUIDs and emits the shared response envelope.
2. `internal/services/restaurant/business/service.go` applies bounds and sort allow-list validation.
3. `internal/services/restaurant/repository/postgres.go` uses parameterized SQL and `ST_DWithin`/`ST_Distance` against `restaurants.location`.
4. `migrations/000023_add_catalog_fields.up.sql` brings the existing brownfield catalog tables up to the fields needed by the read contract.
5. `scripts/seed_demo.sql` inserts deterministic fictional owners/restaurants/menus/items. `make seed-demo` runs it against the local Postgres container.

## Verification

- `go test -race ./...`
- `go vet ./...`
- `git diff --check`
- `docker compose up -d --build` and live PostGIS migration to version 23
- `make seed-demo` twice: five restaurants and six menu items remain present without duplicates
- Live Shamgarh discovery returned five restaurants; live menu returned the seeded Jain/vegetarian catalog; invalid latitude returned HTTP 400

## Known limits

The current migration still preserves the legacy decimal database storage for compatibility; the API read model converts catalog prices to minor units. Cart/order pricing must revalidate current database values server-side. Restaurant owner menu CRUD, image storage, cache invalidation, and full frontend presentation are separate tickets.
