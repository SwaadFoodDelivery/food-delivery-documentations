# ORDER-GRPC-002 — active checkpoint, 2026-09-13

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
