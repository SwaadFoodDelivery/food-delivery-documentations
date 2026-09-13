# Restaurant-owner order queue walkthrough

## Scope

This is a demo/learning workflow. It uses the backend mock delivery boundary and does not connect to a commercial delivery provider.

## Entry point

1. Sign in with a seeded or demo `restaurant_owner` account.
2. From the landing page choose **Open restaurant orders**, or open `/restaurant/orders`.
3. The frontend calls `GET /api/v1/owner/restaurant` to resolve the authenticated owner's active restaurant.

## Queue actions

The view loads the latest 50 orders from `GET /api/v1/restaurants/:restaurantId/orders` and exposes only backend-valid transitions:

- `order_created` or `confirmed` → accept or reject
- `accepted` → start preparing or reject
- `preparing` → mark ready for pickup

Each mutation calls `PATCH /api/v1/restaurants/:restaurantId/orders/:orderId/status`. The backend verifies owner scope, validates the transition, updates `updated_by`, and records the audit/history event.

## Verification

- Frontend: `npm run lint`, `npm test -- --watch=false`, and `npm run build` pass; owner service tests cover bearer headers, lookup, listing, and status mutation.
- Backend: `go test -race ./...`, `go vet ./...`, Docker build/start, and live route registration pass. Unauthenticated access to `/api/v1/owner/restaurant` returns `401`.
- Browser screenshots/persona evidence remain unavailable until a reachable browser-capable deployment is provided.
