# Swaad integrated recovery and persona checkpoint

This supersedes the earlier onboarding/browser checkpoint and historical release
claims. Development is pushed to feature PRs, not merged into main. Original user
changes and the original frontend's damaged Git checkout were preserved.

## Published work and dependency order

| Repository | PR | Feature | Head |
| --- | --- | --- | --- |
| Backend | [11](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/11) | Approval-only access, owned authenticated verified uploads, migration 28 | 7cccda8 |
| Backend | [12](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/12) | Integrated serviceability policy | 5f253ed |
| Backend | [13](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/13) | Payment-gated delivery, cross-key deduplication, interrupted mock retry | c113d3f |
| Backend | [14](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/14) | Latest onboarding application/account serialization | b9ab9de |
| Backend | [15](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/15) | Migration 29 status-history emitters/current order status | f83af60 |
| Backend | [16](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/16) | Operations overview and audit database repairs | d17c155 |
| Frontend | [5](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/5) | Approval-aware onboarding | cd21694 |
| Frontend | [6](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/6) | Customer browser harness/checkout race guards | cf6ed49 |
| Frontend | [7](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/7) | Same-order payment retry/cancellation/reconciliation | 6044a8f |
| Frontend | [8](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/8) | Owner/driver/operations browser acceptance and surfaced UX repairs | 2782bc4 |

Backend stack: main → 11 → 12 → 13 → 14 → 15 → 16.
Frontend stack: main → existing 3 → existing 4 → 5 → 6 → 7 → 8.
Each arrow is a prerequisite, not a merge. Frontend 8 needs the integrated backend
16 for the full acceptance run. Retarget and revalidate downstream PRs when their
parents merge; do not cherry-pick away required migrations or bypass protection.

Why PRs previously appeared absent: agent quota failures left changes local;
those files were recovered and pushed. New feature work now has separate linked
PRs. Looking only at main will not show these unmerged features.

## Correctness work completed

- Submission never grants approval; obsolete draft/rejected applications cannot
  revoke newer approval. Init, submit, resubmit and review lock account first.
- Upload callback is authenticated, owner/draft-scoped and checks actual nonempty
  object storage before marking uploaded. Signed MinIO PUT/HEAD were tested.
- Unpaid/declined/terminal orders cannot acquire or advance delivery. Paid orders
  missing an assignment are retried by persisted worker state. Delivery failure
  cannot turn a committed successful payment into a request to charge again.
- Different payment keys serialize on the same order. Interrupted MOCK pending
  attempts expire after one minute; original key stays failed, a fresh key may
  retry, and late completion cannot resurrect the expired attempt. This expiry
  never applies to a real provider or uncertain real financial charge.
- Checkout retains a placed order across decline/reload and retries that order,
  supports cancellation, and reconciles terminal server status. Failed history
  reads retain retry state. Legacy empty timelines use current order status.
- Migration 16 had defined history functions without attaching them. Migration
  29 attaches order/partition and delivery triggers, records actors and event
  timestamps, suppresses no-ops, and rolls back history with the state change.
  No missing historical events are invented. Down preserves recorded histories.
- Operations real queries no longer pass an extraneous SQL argument; audit DTOs
  have explicit database field mappings. COD labels use the real API enum.

## Verification

- Full backend `go test -race ./...` and `go vet ./...` pass at 2ee653f.
- Real PostGIS payment/delivery/operations suite passes against disposable
  `swaad_history_test_20260911`, including eight-way payment deduplication,
  cancellation, legacy history ownership, overview and audit readback.
- Migration 29 down/up, no-op, actor, chronological multi-transition and rollback
  tests pass. Existing onboarding and serviceability real-database suites also
  pass on the final integrated source.
- Frontend 110 unit tests, lint and production build pass locally and final CI
  revalidates 2782bc4. Existing Sass/PostCSS and 1.6-MiB entry warnings remain.
- All 16 supplemental network-mocked Chromium regressions pass at a40cb6b.
- Final combined real-backend run at d17c155 / 2782bc4: **5 passed in 53.4s**.
  Four customer tests cover delivered journey, outside radius, cancellation via
  history then checkout recovery, and same-order decline/reload/retry. The fifth
  test covers owner, driver, operations, applicant approval and role denials.
  No backend responses were intercepted. Four persona screenshots were captured
  and visually inspected. Login/uploads remain explicit fixture preconditions.

Known CI: backend 13 [34579250462](https://github.com/SwaadFoodDelivery/food-delivery-backend/actions/runs/34579250462),
14 [34579384042](https://github.com/SwaadFoodDelivery/food-delivery-backend/actions/runs/34579384042),
15 [34625805925](https://github.com/SwaadFoodDelivery/food-delivery-backend/actions/runs/34625805925) passed.
Final backend 16 [34626348649](https://github.com/SwaadFoodDelivery/food-delivery-backend/actions/runs/34626348649)
and frontend 8 [34626351667](https://github.com/SwaadFoodDelivery/food-delivery-frontend/actions/runs/34626351667)
are green. Backend includes race/vet, PostGIS suites and Docker build. Frontend
includes 16 mocked browser tests, 110 unit tests, lint, production audit and build.

## Exact local continuation

Authoritative backend checkout:
`../.worktrees/serviceability-policy-backend`, branch `codex/operations-query-repair`.
Authoritative frontend checkout:
`../.worktrees/serviceability-policy-frontend-fresh`, branch `codex/persona-browser-acceptance`.
Fast frontend validation clone: `/tmp/swaad-browser-remote-20260911` (same branch).
Node 22: `/opt/homebrew/opt/node@22/bin`. Documents filesystem reads sometimes
stall; the clean remote clone avoids that. Never reset original user worktrees.

Owned local backend runs on port 18080 through `bash scripts/run-e2e-backend.sh`.
PostGIS database `swaad_e2e_20260911` is schema 29, Redis container
`swaad-e2e-redis-20260911` exposes 16379, NATS `swaad-e2e-nats-20260911` exposes
14222. Existing user's dev backend on 8080 and shared dev data are untouched.

```sh
E2E_LOCAL_SEED=1 E2E_BACKEND_URL=http://127.0.0.1:18080 E2E_PORT=4174 \
  npx playwright test --project=chromium-live --project=chromium-personas
```

Use Node22 on PATH. Local seeding creates a fresh fictional customer per test to
isolate rate limits/cart/address state. Persona seeding creates new role accounts,
restaurant, COD orders/assignments and pending review metadata. Rate limits stay
enabled. All fixtures are in the guarded disposable DB; sessions expire after two
hours and remain in private 0600 files outside Git. Never print or commit tokens.

Live tests do NOT intercept HTTP responses. Login and uploaded document metadata
are preconditions; these tests do not prove browser OTP/login/upload execution.
Persona evidence includes owner, driver, operations and applicant screenshots,
plus token-free JSON. Reports are under `node_modules/.cache/swaad-e2e/` and each
new run replaces the previous report, so copy needed sanitized evidence first.

The combined run uses global rate limit 300/min in the dedicated E2E launcher:
all personas share one loopback IP. Application default 60/min and route/user
limits remain unchanged. This is not acceptance evidence for default rate limits.
Report: `/tmp/swaad-browser-remote-20260911/node_modules/.cache/swaad-e2e/report/index.html`.
Screenshot files under its `data/`: owner `52dd927124772c7384d37361af29a2ce01f8d208.png`,
driver `3912a9290afeb70e5284f5082ea79dc177fcf0d9.png`, applicant
`1bbc5dd3b909ed2921e0fd2909623d0356b9df29.png`, operations
`097215df4f3fe0ce3e53a669ca87518d0018cb81.png`. These are local generated evidence,
not committed credentials or fabricated screenshots.

## Review and remaining scope

Independent agent review at 9bc8ab6/d5dafcb found three P2 issues and no P1. All
three were implemented with regressions. That agent implemented onboarding
serialization, then quota stopped its final rereview. Its own patch is not
independently approved. Later history/operations repairs have orchestrator review
and test evidence, not invented Staff/QA/Architecture/Product approvals.

Main protection was re-read: both repositories require one approval and enforce
it for admins. Agent comments from the PR author's account are not a separate
authorized GitHub approval. No protection changes or merges were made.

Next work: a separate browser OTP/login plus actual document
upload/reject/resubmit feature. Current combined acceptance and CI pass. Review
negative persona failures, error feedback and accessibility before release.
Scheduler/quota telemetry/shared deployment remain unverified optional setup,
not blockers to local demo development. Real providers stay deliberately mocked.
