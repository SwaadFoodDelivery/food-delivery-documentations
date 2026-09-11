# Operations workspace walkthrough

The demo operations workspace is available at `/operations` to the seeded operations manager account:

- Phone: `9000000021`
- Name: `Swaad Demo Operations`
- Role: existing `restaurant_manager` role, used as the demo operations role

The workspace calls `GET /api/v1/operations/overview` and shows order totals, active/delivered counts, driver availability, current assignments, delivery status, restaurant/customer context, and the simulated provider label.

Use the order status filter to narrow the latest 50 orders. Active demo orders expose a cancellation action. Cancellation is manager-only, updates the order and pending delivery state transactionally, and writes an `ops_order_cancelled` audit record. Delivered, rejected, and already cancelled orders cannot be cancelled.

The backend route is protected by JWT authentication and `restaurant_manager` authorization. Unauthenticated access returns `401`; invalid order status filters return `400`. The local Docker health check and route registration were verified after rebuilding commit `c6eb552`.

Frontend commit `1f67d5f` adds the route, service, role guard, responsive view, and service tests. Local legacy Vue Jest/build processes stalled under Node 25; GitHub Actions is the authoritative frontend check for the pushed branch.
