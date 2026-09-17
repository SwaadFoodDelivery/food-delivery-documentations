# DRIVER-EARNINGS-001 implemented — 2026-09-17 (Claude Code session, continued)

Supersedes only the "next exact action" of
[the 2026-09-16b checkpoint](2026-09-16b-auth-lifecycle-003.md); that one's
findings (branch-protection blocker, self-approve decision, CI-glob fix,
AUTH-LIFECYCLE-003 evidence) still stand and are not repeated here.

## What was built

Continuing the "keep implementing" instruction, the next backlog gap was
found by cross-checking the master prompt's persona coverage checklist
against actual code: the Driver section requires "permitted earnings/history,"
and the `driver_availability_earnings` flowchart names `GET
/delivery/partner/earnings` explicitly, but a repo-wide grep for "earning"
returned zero hits anywhere in the backend before this session. Availability
itself was already implemented (via `PUT /users/me/profile`, not the
flowchart's literal `/delivery/partner/availability` path -- an established
prior divergence, not new). Earnings was the real, unimplemented half.

- **Backend** — [PR23](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/23),
  branch `codex/driver-earnings`, stacked on `codex/auth-refresh` (PR22 tip
  `19400c0`), worktree `.worktrees/driver-earnings-backend`. Adds `GET
  /api/v1/driver/earnings` to the existing delivery service's driver route
  group. Confirmed via grep that no commission/payout-rate concept exists
  anywhere in the schema -- `orders.delivery_fee` (customer-facing,
  `DECIMAL(10,2)`) is the driver's payout in this demo economy, so earnings
  = `SUM(delivery_fee)` joined from `deliveries` to `orders` on
  `(order_id, created_at)` for that driver's `delivered` rows. Money
  conversion (`decimalToMinor`/`mustMinor`) is a local copy matching the
  order package's exact algorithm, following this codebase's established
  per-package-duplication convention (order/payment/restaurant/cart each
  already keep their own copy rather than sharing one). Response is a single
  aggregate (`total_earnings_minor`, `delivered_count`, `currency`,
  `demo_label`) -- there is no precedent for cursor pagination on any
  driver-facing read in this codebase (order history's own `cursor` param is
  an explicit unimplemented stub), so a list was deliberately not built.
  Verified: `go build`/`go vet`/`go test -race ./...` full-repo clean, plus a
  **real Postgres integration subtest** added to the existing
  `payment_gate_integration_test.go` (not just mocked) -- drives an actual
  order through `EnsureMockDelivery` and every `UpdateForDriver` transition,
  asserts the aggregate is unchanged while merely `out_for_delivery`, then
  increases by exactly that order's fee once `delivered`, and that an
  unknown driver reads a clean zero. This was run against a real disposable
  `swaad_delivery_earnings_test_20260917` database on the shared
  `food-delivery-postgres` Docker container (already running from another
  session -- reused, not disturbed; started Docker Desktop myself since it
  wasn't running, discovered the container already existed once it was up)
  before pushing, then the disposable database was dropped.
- **Frontend** — [PR12](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/12),
  branch `codex/driver-earnings-ui`, stacked on `codex/auth-session-recovery`
  (PR11 tip `eeb6aed`), worktree `.worktrees/driver-earnings-frontend`. Adds
  a "Your earnings" section to `DriverView.vue` matching the existing
  "Current assignment" card's loading/empty/loaded structure exactly;
  auto-refreshes the moment a delivery reaches `delivered` (in addition to
  its own manual Refresh button) since that's precisely when the payout
  lands in the aggregate. Verified: lint clean, full unit suite 160/160
  (1 new test), production build clean. No new E2E coverage -- disclosed in
  the PR, not left unstated.
- **Process note**: the exploration and first implementation pass were
  accidentally done directly inside `.worktrees/order-identity-backend`
  (PR21's own branch, `codex/order-identity-guard`) instead of a fresh
  worktree stacked on the actual current tip. Caught before committing;
  recovered by saving the uncommitted diff as a patch, restoring that
  worktree to PR21's exact committed state (`git checkout --`), creating the
  correct new worktree/branch off `codex/auth-refresh`, and applying the
  patch there. PR21 was never touched/pushed with this unrelated work --
  verify `git -C .worktrees/order-identity-backend status` reads clean
  against `codex/order-identity-guard` if picking this up cold, as a sanity
  check that the recovery was complete.

## Real live-server verification (both PR22 and PR23)

Beyond the repository-level integration test, ran the actual `codex/driver-earnings`
commit as a live server (`go run ./cmd/server`, dedicated disposable Postgres
database `swaad_e2e_driver_earnings_20260917`, isolated Redis DB index 3 on the
shared Redis instance rather than a separate container, port 18099) and drove
the real HTTP flow with no mocks: seeded a driver + a `delivered` order directly
in Postgres, logged in through the real OTP flow (init-session -> send-otp ->
read the mock OTP from server logs, stripping ANSI color codes with `sed` since
a naive grep initially missed it -> verify-otp) to get a genuine signed access
token, then called `GET /driver/earnings` and got back exactly
`{"total_earnings_minor":3000,"delivered_count":1,"currency":"INR","demo_label":
"SIMULATED EARNINGS..."}` -- matching the seeded ₹30.00 delivery fee precisely.
Also live-verified PR22's `/auth/refresh` in the same session: `X-Client-Type:
web` correctly set the HttpOnly cookie and omitted the token from the body;
refresh-then-logout-then-refresh-again correctly failed `SESSION_NOT_FOUND`;
a *different*, still-active session's access token correctly still worked
against `/driver/earnings` afterward (confirms logout revokes only the
session it's called on, not every session for that user -- correct multi-
session semantics, not a bug, though it briefly looked like one mid-test).
Disposable database dropped and test server process killed afterward; posted
as PR comments on #22 and #23 rather than just this file, so the evidence is
visible where a reviewer would look. First `docker exec ... <<'SQL'` attempt
silently did nothing (no error, no output) because it was missing `-i` --
worth remembering if seeding via heredoc into a container again.

This does NOT cover actual browser rendering of the new `DriverView.vue`
earnings section -- the API contract is now proven correct end-to-end and the
field names match the Vue template exactly (`total_earnings_minor`,
`delivered_count`, `demo_label`), and the production build compiles clean,
but nobody has looked at the rendered page in a real browser. That gap is
smaller and more precisely stated than the original "no E2E coverage" note,
not fully closed.

## Evidence

- Backend PR23 CI confirmed green: all 4 checks (`go-checks`, `docker-build`,
  `migration-integration`, `paired-order-service`) pass at commit `9134fe4`.
- Frontend PR12 CI confirmed green: all 5 checks (`lint`, `unit-tests`,
  `production-audit`, `build`, `Chromium UI (supplemental network mocks)`)
  pass at commit `fdfbe05`.
- Neither PR has had independent Staff/QA review -- automated verification
  only, same caveat as every other PR in the current stack.

## Next exact action

1. Confirm frontend PR12's CI went green (see Evidence above).
2. Re-check `gh pr list` fresh for every repo before picking the next work
   unit -- the user has said they'll self-approve PRs going forward, so
   merge state can change between sessions now in a way it couldn't before.
3. Re-scan the master prompt's persona/journey coverage checklists (§8) and
   the 16 flowcharts against current code for the next genuine gap, the same
   way this session found the earnings gap -- grep for the domain noun
   across the backend before assuming something is unimplemented, and check
   the actual flowchart SVG text labels rather than trusting a title alone.
4. Two structural items still open from the 2026-09-16 checkpoints, not
   engineering work: the branch-protection/self-approval blocker (user-owned,
   in progress by their own choice) and the live SendGrid key sitting
   uncommitted in `food-delivery-backend/.env.development` (user-owned,
   already flagged twice).
