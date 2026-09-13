# ORDER-GRPC-002 — active checkpoint, 2026-09-13

## Latest verified state — 19:35 IST

All list source is committed and pushed. Backend
[PR19](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/19) `ad30cf7`,
service [PR2](https://github.com/SwaadFoodDelivery/order-service/pull/2) `4d5fdd9`,
proto PR2 `dc2e8c6`; drafts while final gates complete. Backend paired CI pins
the immutable service commit and all five SELECT-only table grants.

Root verification: full `go test -race ./...` and `go vet ./...` pass in both
implementation repositories. Service suite with ORDER_RPC_TEST_DATABASE_URL
passed actual GetOrder/GetUserOrders PostGIS tests. Backend
TestPairedOrderServicePostgres passed with a separately running real list service:
ties, exact microseconds, new insert between pages, final and empty lists,
foreign/tampered cursors, role denial and dependency-key failure. Test identity
is explicitly seeded middleware context, not browser authentication.

Final independent Staff/Architecture review at these commits found no blocking
P1/P2; source inspected, test results supplied by root. Its nil-client fallback
hardening is fixed and has a registered-route regression test. Final QA/Product
review and backend/service CI still pending. Proto CI34749001931 is green.

The new owned process is session53719, binary
`/tmp/swaad-order-list.kzEcmO/order-service`, loopback15052. Database is the existing
disposable `swaad_grpc_test_20260912`, schema29. Dedicated login
`swaad_grpc_list_reader_20260913` has SELECT only on all five tables. No private
credentials are persisted in these docs. Old service15051 remains untouched.
Tests added fictional records only in the disposable DB. Temporary test roles
were removed by test cleanup; the new runtime login remains for continued testing.

Implementation/QA workers temporarily returned usage-limit errors before root
verification. Tesla's service commit was recovered and pushed by root. Later
manual continuation resumed reviewers; there is no numeric quota telemetry or
guaranteed automated reset. Current source is saved remotely even if cut off.
No feature is merged/released and no GitHub formal approval is fabricated.

The detailed implementation snapshot below is historical to the first save;
this latest section takes precedence. Next: complete CI/QA evidence, post labeled
agent review comments, then the bounded customer pagination UI within this same
vertical feature. Do not start unrelated work or transfer order writes yet.

Prior feature remains complete, CI-green, ready and unmerged: see
[GetOrder handoff](2026-09-13-order-grpc-handoff.md). This checkpoint supersedes
its next-action instructions, not its evidence.

## Current scope and files

Authenticated, owned client order-list summaries over GetUserOrders. Strict RPC
limits 1–50 (HTTP default 20), keyset `(created_at, order_id)` descending, signed
versioned cursor bound to owner and exact timestamp, at most 1024 bytes. SQL
ownership is independent of cursor validation. Return the existing HTTP history
summary fields plus next_cursor; no list item/payment hydration or global count.
Preserve orders without delivery and current names of inactive restaurants.
Delivery joins use both partition keys. Backend remains the writer/migrator.

- Proto `.worktrees/order-grpc-proto`, branch `codex/order-list-contract`, pushed
  `dc2e8c6`; [PR2](https://github.com/SwaadFoodDelivery/proto/pull/2), draft, base
  `codex/order-grpc-contract`. Race/vet pass. Module version
  `v0.0.0-20260913090154-dc2e8c6d6c1d`.
- Backend `.worktrees/order-grpc-backend`, branch `codex/order-list-read`, base
  `a27ee6e`, active root-owned changes. Typed list client/handler, disabled-mode
  cursor rejection, summary/error/identity/deadline tests; focused race tests pass.
  Paired real service/PostGIS pagination and final CI still pending.
- Service `.worktrees/order-grpc-service`, branch `codex/order-list-service`, base
  `3808d06`; implementation agent Tesla owns edits. Do not concurrently edit it.
  New list reader requires SELECT on restaurants/deliveries in addition to the
  original three tables; every table must remain write-denied.
- Staff/Architecture Hubble reviewed plan; reviewing backend diff read-only.
  QA/Product Mill supplied a scope-based forecast, not a formal approval.

## Resume safely

1. Inspect current git status and agent state before edits. Original dirty user
   backend/frontend repositories are out of scope; preserve them.
2. Finish service/list and root backend paired acceptance. Existing service
   session73379 on 15051 remains the old GetOrder binary. Use a separate owned
   list runtime on 15052 with a new SELECT-only role; do not overwrite it.
3. Shared PostGIS container is food-delivery-postgres; use only dedicated
   `swaad_grpc_test_20260912` or another validated disposable swaad_grpc_test_*
   database. Serialize fixture changes with Tesla. Never use the normal app DB.
4. Publish separate draft service/backend PRs on their previous feature branches,
   pin immutable paired service source in CI, obtain independent role reviews,
   resolve findings and verify green CI before ready. Deployment service-first;
   rollback backend-first. No cursor fallback to local first page.
5. Commit/push each completed gate and update this file/STATE/RESUME with actual
   heads, tests and remaining findings.

## Merge and estimate

GitHub was rechecked: order-service PR1 is open, mergeable but blocked by required
review. Agents can provide qualified review evidence, not another authorized
GitHub identity. No self-approval, fabricated identity or protection bypass.
PR creation initially hit transient GraphQL/REST errors; REST retry successfully
created proto PR2. This was not lost code: its branch was already pushed.

[Forecast](../DELIVERY_FORECAST.md): full agreed mock/learning scope 18–30 active
days (~4–6 weeks at five productive days/week), usable local consolidation 2–4
days. These are provisional scope ranges, not measured current velocity; waiting
for approvals/access/quota is additional. No production-scale guarantee.
