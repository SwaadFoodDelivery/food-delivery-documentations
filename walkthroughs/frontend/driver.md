# Mock driver dashboard walkthrough

## Scope

This is a demo/learning workflow. Driver assignment and progression are simulated by the backend; no live courier provider, GPS, or commercial dispatch is connected.

## Entry point

1. Sign in with a seeded or demo `driver` account.
2. From the landing page choose **Open driver dashboard**, or open `/driver`.
3. Toggle availability and save the demo city. Availability is persisted through `PUT /api/v1/users/me/profile`.

## Assignment and progression

The backend assigns the next available, non-busy fictional driver when a customer order enters the delivery flow. The dashboard loads the driver's active assignment from `GET /api/v1/driver/delivery`.

The driver can advance only one state at a time through `PATCH /api/v1/driver/delivery/status`:

`assigned` → `en_route_to_restaurant` → `arrived_at_restaurant` → `picked_up` → `out_for_delivery` → `delivered`

Each action updates the delivery and order actor, records trigger-backed status history, and schedules the mock worker's fallback transition. A driver cannot skip states or update another driver's assignment.

## Verification

- Backend: race tests, vet, Docker build/start, health, and protected route registration pass.
- Frontend: lint, 53 unit tests, and production build pass; service tests cover bearer auth, availability, assignment lookup, and progression.
- Browser screenshots/persona evidence remain unavailable until a reachable browser-capable deployment is provided.
