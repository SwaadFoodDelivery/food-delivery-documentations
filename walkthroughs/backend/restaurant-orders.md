# Restaurant order operations

Status: backend API implemented at commit `684d50a`; role-specific owner UI is the next persona slice.

Restaurant owners can read the latest 50 orders for their own restaurant:

```http
GET /api/v1/restaurants/{restaurantId}/orders
Authorization: Bearer <restaurant-owner-session>
```

They can move an order through the preparation workflow:

```http
PATCH /api/v1/restaurants/{restaurantId}/orders/{orderId}/status
Authorization: Bearer <restaurant-owner-session>
Content-Type: application/json

{"status":"preparing"}
```

The route is protected by the `restaurant_owner` role and the repository repeats the ownership predicate against `restaurants.owner_id`. Valid transitions are:

```text
order_created/confirmed → accepted or rejected
accepted                → preparing or rejected
preparing               → ready_for_pickup
```

Every update sets `orders.updated_by`, lets the existing order-status trigger record history, and writes an owner audit row. Invalid transitions return a conflict; another owner’s restaurant is not exposed.

Verification: `go test -race ./...`, `go vet ./...`, Docker build, clean migration state, health check, and live Gin route registration all passed for the checkpoint.
