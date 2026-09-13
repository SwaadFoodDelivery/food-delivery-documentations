# ORDER-GRPC-001: staged order-service integration

Status: architecture plan reviewed by an independent agent, implementing the
first read-only slice. No formal GitHub approval or completed writer extraction
is implied.

## Evidence and problem

At order-service `ce30cf2` and proto `c26b9ac`, both repositories contained only
bootstrap/CI commits. gRPC registration was a TODO, business/repository methods
returned nil, and backend gRPC methods echoed their inputs. The backend HTTP
order routes never invoked that client. `gh` confirmed no order-service PRs.
Existing demo checkout was implemented directly in the backend transaction.

That transaction validates cart/menu/address/serviceability, snapshots prices,
inserts order/items/notification, and converts the cart. Payment, restaurant and
delivery code also share order state. Blindly moving only the insert would split
the transaction and create competing owners or incomplete records.

## Decision

First implement authenticated, owned `GetOrder` over actual gRPC. Backend remains
the sole migration and write owner. Order-service reads the same schema 29 using
read-only transactions and a SELECT-only deployment credential; it must never
run its copied legacy migration tree. No second orders database, Kafka workflow,
or cross-database atomicity claim is introduced.

Keep protobuf package, RPC methods, field numbers and enum values compatible.
Publish generated Go bindings at a pinned canonical module version. Add integer
minor-unit currency fields; old doubles retain tags as deprecated compatibility
fields. Read persisted snapshots and join items by both order ID and creation
timestamp, respecting the partitioned composite key.

Authenticate the calling backend with a private service key before processing
requests. Backend delegates its authenticated user UUID/role, never HTTP body or
query identity. Restrict this first RPC to client-owned records; foreign and
missing return identical NotFound. No arbitrary role-based privileged bypass.

Plaintext transport is limited to explicit development/test numeric loopback
endpoints. Reject public/production configuration until TLS/mTLS is implemented.
Use bounded RPC deadlines and fail closed, with no local read fallback. Register
new HTTP GET /orders/:id only when explicitly enabled. Dependency credential
failure maps to 502, not a customer refresh-triggering 401. Other unimplemented
RPCs return Unimplemented, never fabricated success.

## Validation and future handover

Contract generation drift/wire tests; auth, identity, money, timeout and error
tests through real transport; actual PostGIS owned/foreign reads; no writes by
the service; and paired backend → service → database acceptance are required.
Keep the first PR draft until those gates and independent review pass.

Later move order writes only after designing ownership for carts, catalog and
address validation, idempotency, notification/outbox atomicity, payment/delivery
updates, and database migrations. Keep existing checkout regression gates during
that handover. This staged approach preserves the working demo and makes the
current architectural gap explicit rather than treating stubs as implemented.
