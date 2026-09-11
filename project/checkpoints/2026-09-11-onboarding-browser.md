# Recovery checkpoint — 11 September 2026

## What happened to the developed parts

Agent usage limits interrupted work before commits. The orchestrator recovered
the local files, fixed interrupted tests plus a real SQL mapping defect, and
pushed the reviewed implementation units. Nothing was deleted from original
user worktrees and no separate GitHub reviewer identities were created.

| Feature | Branch / commit | GitHub |
| --- | --- | --- |
| Backend onboarding approval + authenticated verified uploads | codex/vert-001-catalog / 7cccda8 | [PR 11](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/11) |
| Integrated serviceability + parent fixes | codex/serviceability-policy / 5f253ed | [PR 12](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/12) |
| Frontend pending/rejected/approved UX | codex/onboarding-review-ui / cd21694 | [PR 5](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/5) |
| Browser journey + quote mutation races | codex/browser-e2e / 65ab83f | [Draft PR 6](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/6) |

Frontend PR 5 is stacked on existing serviceability PR 4; PR 6 is stacked on 5.
Backend PR 12 is stacked on 11. Main remains protected; none of these are merged.

## Verified

- Backend full `go test -race ./...`, `go vet ./...`, formatting/diff checks.
- Real PostGIS migrations through 28, migration repair idempotency and preservation
  of approved/seeded users, ownership/storage/state regression suite, concurrent
  eight-way submit producing one transition/audit, stale-CAS rollback.
- Actual MinIO signed PUT and HEAD, empty object refusal, signed deletion of the
  single generated fixture confirmed by 404. No shared objects were deleted.
- Backend CI [34549461722](https://github.com/SwaadFoodDelivery/food-delivery-backend/actions/runs/34549461722) green: race/vet/format, migration/onboarding integration, Docker build.
- Integrated PR 12 PostGIS serviceability tests pass after resolving parent merge
  conflicts without dropping closure/radius checks or database CI.
- Frontend 13 unit suites / 110 tests pass, including pending review, returning
  applicants, router gates, rejection/resubmission and authenticated callbacks.
  Lint passes. Browser/production-build results are not yet claimed at this checkpoint.

## Active work and exact continuation

1. Finish browser execution. Playwright files and README are committed under
   `tests/e2e`; `npm run test:e2e` is real-backend acceptance with NO network
   interception, `npm run test:e2e:mock` is supplemental network-mocked coverage.
   The first local Vue startup timed out at 180s; process sampling showed blocked
   dependency filesystem reads. Node 22 is `/opt/homebrew/opt/node@22/bin/node`.
   Clean remote clone validation path: `/tmp/swaad-browser-remote-20260911`.
   A local clone attempt at `/tmp/swaad-browser.fGNuIF` may still be pending.
2. Run `npm ci` only after clone completion, install Chromium, then mock tests and
   real tests. Fix failures, sync commits back to the authoritative frontend
   checkout, update PR 6 body/evidence and readiness honestly.
3. Real backend is running from `.worktrees/serviceability-policy-backend` on
   `http://127.0.0.1:18080`, database `swaad_e2e_20260911` (local Postgres 5432),
   dedicated containers `swaad-e2e-redis-20260911` (16379),
   `swaad-e2e-nats-20260911` (14222). Schema28 and fictional catalog are applied.
   Providers/payment are mock; delivery is accelerated to 30 seconds for tests.
   Existing user's dev backend on8080 and databases/volumes are preserved.
4. Private two-hour auth fixture `/tmp/swaad-e2e-session-20260911-r2.json` exists;
   never commit/print its token. If expired, use committed `tests/e2e/seed-session.cjs`
   with a new E2E_AUTH_FILE. It provisions a new fictional approved client and
   outside-radius address only in the explicitly named test database.
   Run `E2E_BACKEND_URL=http://127.0.0.1:18080 E2E_AUTH_FILE=<private-file> npm run test:e2e`.
5. Concrete next correctness feature: order placement currently creates/schedules
   delivery before prepaid payment success. Worker must not deliver unpaid or
   declined orders. Payment retry must reuse an already-placed order, not a
   consumed cart. Implementation agent hit usage limit BEFORE editing this;
   this remains a known defect, not an accepted deferral or user setup blocker.
6. Independent Staff/Architecture review was interrupted by usage-limit errors.
   Middleware/storage implementation agents supplied scoped tests but no fresh
   full independent approval exists. Resume agent review when available or
   explicitly label orchestrator review; never fabricate formal approvals.

## Recovery and ownership

Authoritative implementation checkouts are the `.worktrees` paths in STATE.json.
Original backend has user's env/workflow/Postman/README/DS_Store changes; original
frontend has user's workflow/README changes and previously corrupt Git objects.
Do not reset, stage, overwrite or repair those as part of this work.

All four implementation commits in the table are pushed. Documentation is being
checkpointed on its existing branch/PR. No real provider onboarding, paid API,
new accounts, or production deployment was attempted. GitHub `gh` works.
Quota percentage visibility is unavailable; agents did return explicit usage
limit failures. No automatic quota-aware scheduler is verified. Saving this
checkpoint permits manual continuation; it does not promise autonomous restart.
