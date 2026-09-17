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
Payment, restaurant transitions, cancellation, timeline history, delivery and migrations
remain backend-owned. Other order-service RPCs explicitly return Unimplemented.
Do not run the service's historical copied migration tree. Before moving writes,
resolve the transaction and ownership handover described in the linked ADR.

## Evidence and reproducibility

Proto PR2, order-service PR2 and backend PR19 contain the current source/tests and setup
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

## Owned paginated lists (ORDER-GRPC-002 API)

Implemented at proto dc2e8c6, service4d5fdd9 and backendad30cf7; ready/unmerged.
The enabled backend also delegates `GET /api/v1/orders?limit=20&cursor=...` through
GetUserOrders. It keeps the existing `data.orders` summary fields and adds
`data.next_cursor`. Missing/empty cursor starts a first page; empty returned cursor
means no next page. Limits are strict1–50, with HTTP default20. No global count,
item hydration or payment lookup is performed for each summary.

Service `business/cursor.go` signs a versioned owner UUID, creation timestamp and
order UUID with a domain-separated HMAC key. Tokens are opaque to clients, not
encrypted, at most1024 bytes; malformed/foreign/tampered tokens produce400 before
SQL. Key rotation invalidates outstanding cursors. PostgreSQL independently
filters owner and selects `(created_at,order_id)` descending with a strict tuple
boundary and limit+1 lookahead. A cursor comes from the last returned row only
when an extra row exists. UTC microseconds are preserved. Newer orders appear on
a refreshed first page; this is ordered pagination, not a cross-request snapshot.

`repository/list.go` joins current restaurant names without filtering inactive
restaurants, and delivery by both order ID and creation timestamp. Missing delivery
remains valid. The SELECT-only role now needs restaurants/deliveries plus
orders/order_items/payments; all write privileges remain denied. Both the service
and paired HTTP/PostGIS suites verify ties, microseconds, inserted rows, terminal
and empty pages, ownership/cursors and exact paise. Existing customer/persona
browser baseline passes with delegation enabled (5 scenarios); pagination UI and
the newly discovered ambiguous-ID action fix have separate pending gates.

Deploy service and grants first, then the enabled backend. Roll back backend first,
then service/grants. Disabled mode keeps its local first page but rejects any
nonempty cursor with400; clients must explicitly restart rather than silently
duplicate page one. No migration or order rewrite is needed. Next writer extraction
must preserve these boundaries rather than copying a second orders database.
