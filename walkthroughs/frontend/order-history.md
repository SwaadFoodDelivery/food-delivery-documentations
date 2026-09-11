# Customer order history walkthrough

The signed-in customer can open `/orders/history` to see the latest recipient-owned orders, amount, payment method, restaurant, current order/delivery status, and persisted status timeline.

Active demo orders show `Cancel demo order`. The backend verifies ownership and rejects cancelled, rejected, or delivered orders. A successful cancellation updates the order to `cancelled`, clears any pending mock delivery transition, records the existing order-history/audit entries, and adds an in-app notification.

The list and timeline are backed by:

- `GET /api/v1/orders?limit=20`
- `GET /api/v1/orders/:orderId/history`
- `PATCH /api/v1/orders/:orderId/cancel`

This is mock-provider behavior for the learning application; no live courier or payment reversal is performed.
