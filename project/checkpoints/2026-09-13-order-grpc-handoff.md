# ORDER-GRPC-001 — real owned order read

This supersedes the WIP checkpoint. Original dirty user checkouts are untouched.
Code is published in feature PRs; no protected-main merge has been performed.

## Visible GitHub work

| Repository | PR | Branch | Head |
| --- | --- | --- | --- |
| proto | [1](https://github.com/SwaadFoodDelivery/proto/pull/1) | codex/order-grpc-contract | f946f9d |
| order-service | [1](https://github.com/SwaadFoodDelivery/order-service/pull/1) | codex/order-grpc-foundation | 3808d06 |
| backend | [18](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/18) | codex/order-grpc-read | a27ee6e |
| frontend auth | [9](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/9) | codex/auth-onboarding-browser | e660318 |
| backend auth | [17](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/17) | codex/mock-otp-outbox | 214b4f6 |

Why order-service had no development PRs: it really was still the scaffold at
ce30cf2. Registration was TODO, repository/business methods returned nil, and the
backend gRPC client echoed inputs. The working demo checkout wrote directly via
the backend repository. That gap is now explicitly recorded and the first real
service integration is implemented. Old personal origin URLs redirect to the
organization; no GitHub connector is needed for this work.

## Implemented boundary, not full write extraction

Owned client GET /api/v1/orders/:id is opt-in with ORDER_GRPC_REQUIRED=true and
uses a typed authenticated GetOrder RPC to the separately running service. The
backend delegates only middleware-derived identity. The service authenticates
metadata first, validates UUID/client role, filters SQL ownership, and reads
stored order/item snapshots using the partitioned composite key. Missing and
foreign are identical NotFound. Decimal amounts become exact integer INR paise.

The service uses SELECT-only credentials and read-only repeatable-read
transactions. It does not migrate schema or initialize Redis/Kafka. Plaintext is
limited to explicit development/test numeric loopback endpoints. Bounded startup,
query/RPC deadlines and graceful shutdown are implemented. Dependency failures
fail closed; no local fallback or echo-success remains.

Backend still owns order creation, cart conversion, payment/cancellation,
restaurant/delivery changes, notifications and migrations. All other service
RPCs return Unimplemented. Follow the ADR before any writer extraction; do not
duplicate the database or split checkout atomicity by moving only an insert.

## Tests and reviews

- Full Go race/vet pass in backend, order-service and proto.
- Service real PostGIS/SELECT-only-role test passes: exact .05 values, stored
  names, composite item/payment isolation, owner/foreign/missing handling and
  independent lack of INSERT/UPDATE/DELETE/TRUNCATE privileges on all three tables.
- Paired HTTP → actual service process → PostGIS passes twice locally, including
  wrong service credential mapping to 502 and unchanged order values. HTTP
  middleware identity is an explicit fixture; JWT/session handling is not tested
  again by this gate. The auth browser feature remains separate evidence.
- Backend CI [34733448062](https://github.com/SwaadFoodDelivery/food-delivery-backend/actions/runs/34733448062)
  passes all four jobs including the new paired service gate. It builds service
  05f99a6; latest service3808d06 only strengthens tests, no production-code change.
- Proto CI [34708461039](https://github.com/SwaadFoodDelivery/proto/actions/runs/34708461039) passes.
- Service initial CI [34733371050](https://github.com/SwaadFoodDelivery/order-service/actions/runs/34733371050)
  passes all jobs; final-head CI34733579490 also passes Go/PostGIS and Docker.
  Proto1, order-service1 and backend18 are all marked ready for review.
- Independent Staff/Architecture rereview found no blocking P1/P2 in the current
  integration. QA ran unit/race checks and found two regression gaps; both were
  fixed in3808d06 and independently rereviewed without remaining blockers.
  These are agent reviews, not human or formal GitHub approvals.

Auth feature: real OTP/MinIO/review/offline replacement flow passed;118 unit
tests and16 mocked browser tests pass. Frontend final docs-only head e660318 CI
34733482349 passes. Private counts-only auth reporting and synthetic failure
artifact cleanup are verified; the older unsafe HTML report was removed.

## Resume / runtime state

Authoritative current worktrees:

- ../.worktrees/order-grpc-backend (a27ee6e)
- ../.worktrees/order-grpc-service (3808d06)
- ../.worktrees/order-grpc-proto (f946f9d)
- ../.worktrees/serviceability-policy-frontend-fresh (e660318)
- ../.worktrees/serviceability-policy-backend (214b4f6; auth runtime source)

Only the new disposable PostGIS database swaad_grpc_test_20260912 was provisioned
for this feature, using backend migrations29 and fictional demo catalog. Fictional
test users/orders/payments remain there. Temporary NOLOGIN reader roles created
by integration tests were revoked and dropped. One local service login role
swaad_grpc_reader_20260913 has SELECT on orders/order_items/payments only.

Owned order-service runs at127.0.0.1:15051, session73379, using that SELECT-only
role and disposable database. Verify process ownership/health before reuse or
stopping. Its source was05f99a6;3808d06 is test-only. Existing auth backend18080
and ordinary dev backend8080 were not stopped or reconfigured. Auth backend uses
dedicated Redis DB1; legacy session-seeded customer/persona helpers useDB0.

Local paired rerun: set ORDER_RPC_E2E_DATABASE_URL to the disposable DB,
ORDER_RPC_E2E_ADDR=127.0.0.1:15051 and matching ORDER_RPC_E2E_KEY, then run
go test -race ./internal/services/order/api -run TestPairedOrderServicePostgres
-count=1 -v in the backend worktree. See repo setup docs for exact environment
names; no private credentials, OTPs or JWTs are saved in this checkpoint.

## Next action and remaining limitations

Final CI, review evidence, PR readiness and this checkpoint are saved. Next
bounded order-service scope: design and implement
client order-list reads through gRPC with explicit cursor/ownership contracts,
or begin the reviewed write-ownership extraction plan. Do not claim PlaceOrder
has moved yet. Preserve current backend checkout regression gates.

All four application/contract repositories require one authorized approving
review and enforce protection for admins (order-service/proto checked September13).
The connected author cannot self-approve; do not bypass or alter protection.
Agent review comments are evidence only. Development can proceed on feature
branches; main merge needs the permitted separate reviewer.

Quota visibility unavailable. Earlier agents hit usage limits; later bounded
rereviews succeeded. That is not quota-percentage telemetry or a reset guarantee.
No scheduler/automatic restart/shared deployment/real provider is claimed.
