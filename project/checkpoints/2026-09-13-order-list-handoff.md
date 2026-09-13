# ORDER-GRPC-002 — active checkpoint, 2026-09-13

## 2026-09-14 01:18 IST — resumed and saved

Manual user continuation recovered another worker usage-limit interruption.
Both workers first committed and pushed all recovered changes:
frontend `24a613b` (implementation57c1e01, mocked browser WIP) and backend identity
guard `448e835` (WIP pending verification). Current branches unchanged. Workers
resumed finishing tests/PRs; Hubble reviewing identity diff, Mill reviewing UI
read-only. Do not confuse WIP snapshots with passed release gates.

Root actual Chromium baseline now passes **5 scenarios in54.3s** using stable
frontend e660318 from `/tmp/swaad-browser-remote-20260911`, backendad30cf7 on18081,
service4d5fdd9 on15053 and the new dedicated browser DB, gRPC enabled. Customer
checkout→delivery, outside-radius rejection, declined-order cancellation through
history, same-order payment retry, owner/driver/operations workflows pass. Auth
is seeded through existing guarded helpers; external providers are backend mocks,
no API responses intercepted. This proves existing journeys with delegation,
not the unfinished pagination UI. Node22.23.2, E2E_PORT4175; server exited normally.

Browser DB/runtime handed to frontend worker for targeted live pagination tests;
serialize further root changes. Fastclone remains clean e660318 but worker is
allowed to switch it to the published UI branch for faster testing; verify state
before reuse. Existing source checkouts and original user changes remain intact.

## Latest active work — 19:42 IST (takes precedence)

API subtask gates now pass: backend CI34761457655 (all4), service CI34761365147
(all3), proto CI34749001931. Independent Staff/Architecture and QA/Product no
blocking current-scope findings; QA independently ran service race suite and
backend client/API race tests. Root verified runtime grants on all5 tables:
SELECT true, INSERT/UPDATE/DELETE/TRUNCATE/REFERENCES/TRIGGER false. Labeled agent
evidence comments posted; proto2/service2/backend19 ready, NOT merged/released.

Two implementation writers now own isolated scopes:

- Herschel: frontend `.worktrees/serviceability-policy-frontend-fresh`, branch
  `codex/order-history-pagination`, basee660318. Pagination UI/service/tests:
  append/retry/restart, no stale refresh/timeline writes, preserve loaded rows,
  composite raw-timestamp keys, accessible status and duplicate-ID action guard.
  Also asked for safe real browser acceptance with seeded sessions, no response
  interception, trace/token artifacts disabled. Root does not edit this scope.
- Tesla: NEW backend `.worktrees/order-identity-backend`, branch
  `codex/order-identity-guard`, basead30cf7. [Issue20](https://github.com/SwaadFoodDelivery/food-delivery-backend/issues/20).
  P1 discovered by Staff: old cancellation UPDATE can affect multiple same-owner
  UUID partitions. Approved bounded fix rejects owner-scoped LIMIT2 ambiguity
  with409 before mutation, uses selected composite UPDATE with exactly1 affected
  row, and keeps history anchored to selected identity. Actual PostGIS and
  deterministic concurrent-insert regressions required. This is not accepted
  debt or a claim of global UUID uniqueness. Root does not edit this scope.

Root created a NEW dedicated browser database `swaad_e2e_order_list_20260913`;
backend migration/seed test passes at schema29. Read-only service15053 is session
31715, same4d5fdd9 binary `/tmp/swaad-order-list.kzEcmO/order-service`, using
SELECT-only login `swaad_grpc_list_reader_20260913` with grants in this new DB.
Backend18081 is session40026, binary `/tmp/swaad-order-list.kzEcmO/backend` from
ad30cf7, RPC enabled pointing15053, dev/mock providers and REDIS_DB=0 on dedicated
16379, NATS14222, MinIO9000. GET `/api/v1/health` passes (`/health` was a harmless404).
Fictional development config follows scripts/run-e2e-backend.sh but overrides
port/database/RPC settings; no production secrets used. Do not run that script
unmodified: it hardcodes18080, olderE2EDB and RPCfalse.

Next root action: verify both worker handoffs, tests and draft PRs; independent
reviews; rebuild/restart ONLY owned backend18081 to identity fix; run combined
frontend real list acceptance against18081/15053. Use E2E_DATABASE above and
existing guarded seed-session RedisDB0 helper. Existing15051/15052/18080 and
normal app DB are untouched. New backend's workers can mutate only this new
disposable browser DB. Service/identity unit-PG tests remain in separate
`swaad_grpc_test_20260912` to avoid fixture races.

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
