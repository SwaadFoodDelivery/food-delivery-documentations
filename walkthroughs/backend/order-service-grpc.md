# Actual order-service boundary

Implemented for the local learning demo: a customer-owned order snapshot read
crosses the backend's HTTP API, the generated gRPC client, the separately running
order-service, and PostgreSQL. This replaces the old no-op scaffold. It does not
claim that order creation or all order operations have been extracted.

```mermaid
sequenceDiagram
  participant C as Client
  participant B as HTTP backend
  participant O as Order-service
  participant P as PostgreSQL
  C->>B: GET /api/v1/orders/:id (normal authenticated session)
  B->>B: Require client role, validate order/user UUID
  B->>O: GetOrder + trusted requester + private service metadata
  O->>O: Authenticate service, validate client identity
  O->>P: Read-only consistent snapshot; filter order by owner
  O->>P: Read item snapshots using order ID AND creation time
  P-->>O: Persisted decimal amounts and stored item fields
  O-->>B: Typed protobuf response, exact integer paise
  B-->>C: Existing JSON envelope; sanitized failure if dependency fails
```

## Responsibilities and security

Backend `internal/services/order/api/grpc_read.go` derives identity only from
middleware context, never request body/query fields. Its client in
`internal/grpc/client/order_client.go` applies the service credential and a bounded
deadline. `ORDER_GRPC_REQUIRED=true` enables the new route and requires a startup
connection; false leaves it absent. A dependency outage does not fall back to a
different local read implementation. Dependency credential failure maps to 502,
not a customer-session 401.

Order-service `internal/grpc/server/server.go` registers the generated service.
Interceptors authenticate metadata before input validation, bound request work,
and sanitize panics. Business logic allows only a client requester. Repository
`internal/services/order/repository/postgres.go` uses an owner-filtered query and
repeatable-read, read-only transaction. A SELECT-only database role is the second
boundary. Missing and foreign records return identical NotFound. Item snapshots
are joined by the partitioned composite order key, not just the UUID.

Decimal money is parsed as digits, never multiplied as floating point; `0.05`
becomes exactly five paise. Proto retains old float field numbers for compatibility
but adds authoritative integer amounts and currency. HTTP only exposes integers.
The current status and successful-payment priority come from stored state.

Plaintext gRPC is restricted to explicit development/test and numeric loopback
addresses. No shared/public deployment or TLS support is claimed. Credentials
belong in process environment, not source, browser bundles or reports. Startup
checks PostgreSQL and never runs migrations, Redis or Kafka initialization.
Graceful shutdown has a three-second cap.

## What remains with the backend

Checkout still validates cart, prices, address and serviceability, inserts
order/items/notification and converts the cart within its current transaction.
Payment, restaurant transitions, cancellation, history, delivery and migrations
remain backend-owned. Other order-service RPCs explicitly return Unimplemented.
Do not run the service's historical copied migration tree. Before moving writes,
resolve the transaction and ownership handover described in the linked ADR.

## Evidence and reproducibility

Proto PR1, order-service PR1 and backend PR18 contain the source/tests and setup
commands. Each consumer pins the exact generated module commit. See
`project/RELEASE_MANIFEST.json` and the current checkpoint for tested heads/CI.

- Service unit tests use generated clients over bufconn and real loopback sockets
  for credentials, role/UUID rejection, deadlines, cancellation, errors, panic
  handling and unimplemented methods.
- Service PostGIS integration uses the backend's actual schema29 and fictional
  catalog, temporary SELECT-only role, duplicate UUID/different creation-time
  fixtures, exact .05 values, stored-name snapshots and ownership denials.
- Backend paired integration starts the real service with a SELECT-only login
  and checks HTTP→gRPC→PostGIS owned reads, foreign/missing denial, invalid role,
  wrong service credential and unchanged database values. HTTP auth context is
  an explicit fixture; this is not a new JWT or browser login test.
- Both repositories run these real-database gates in CI against disposable
  `swaad_grpc_test_*` databases. Fixture records are retained only in those
  disposable databases; temporary test roles are revoked and removed.

Rollback: disable the new read route and stop the service. No migration or order
rewrite is needed. Existing checkout remains available. Next writer extraction
must preserve these boundaries rather than copying a second orders database.
