# Backend cart walkthrough

## Scope

VERT-002 adds the first persistent cart slice for authenticated users:

- `POST /api/v1/cart` creates a cart when `cart_token` is absent and adds an available item.
- `GET /api/v1/cart/{cartToken}` returns persisted items and a server-computed INR minor-unit subtotal.
- `DELETE /api/v1/cart/{cartToken}/items/{cartItemId}` removes one item.
- `DELETE /api/v1/cart/{cartToken}` clears items and releases the restaurant binding.

Cart tokens use the opaque format `cart.<base64url(cart UUID)>.<HMAC-SHA256>`. The user identity from the JWT is checked again in every repository query, so a valid token cannot be used to read another user’s cart. Adds lock the cart and item rows in one Postgres transaction; prices are read from `menu_items` at add/read time and never accepted from the client.

## Verification

On 2026-09-08, the local Docker stack passed:

- authenticated add/read/clear flow;
- subtotal `43800` minor units for two `219.00` items;
- forged token `401`;
- restaurant mismatch `409`;
- owner read `200`, cross-user read `401`;
- empty-cart read after clear with zero items;
- `go test -race ./...`, `go vet ./...`, and `git diff --check`.

This is a focused backend checkpoint. It does not implement order quote/place, payment, delivery, frontend integration, Redis cart caching, or independent staff review.
