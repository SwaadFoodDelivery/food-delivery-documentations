# Mock payment boundary

Status: implemented for the learning/demo environment at backend commit `df1a960`. This is a deterministic development adapter, not a commercial payment integration.

## Contract

The authenticated customer sends:

```http
POST /api/v1/orders/{orderId}/payment
Authorization: Bearer <session-token>
Idempotency-Key: payment-demo-001
Content-Type: application/json

{"payment_token":"demo-token"}
```

The endpoint verifies that the order belongs to the authenticated user, creates a pending payment row, calls the configured mock provider, and persists the final outcome. Reusing the same idempotency key replays the stored result. A key cannot be reused for another order.

## Demo behavior

- `PAYMENT_PROVIDER=mock` is the safe default.
- Any ordinary token succeeds and returns a synthetic `mock_pay_*` provider reference.
- `payment_token: "mock_fail"` or `"decline"` returns HTTP 402 with `PAYMENT_DECLINED` and persists a failed outcome.
- `cash_on_delivery` orders reject payment attempts with `PAYMENT_NOT_REQUIRED`.
- Cancelled, rejected, and delivered orders reject payment attempts.
- `PAYMENT_PROVIDER=real` fails at startup because no commercial adapter is implemented; mock success never masquerades as real payment.

Migration `000027_mock_payment_fields` records the provider, synthetic provider reference, failure code, and safe failure message. Raw payment tokens are never persisted.

## Verification

- `go test ./...` passed.
- `go test -race ./...` passed.
- `go vet ./...` and `git diff --check` passed.
- Docker startup applied migration 27, registered the payment route, and returned a healthy `/api/v1/health` response.
- GitHub Actions Go checks and Docker build passed for PR #11.

The frontend checkout caller and any real provider remain separate follow-up work.
