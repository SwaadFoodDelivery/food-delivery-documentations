# Session log

## 2026-09-08 — initial recovery audit

- Confirmed local host is macOS/Darwin arm64 at `/Users/rishabhjain/Documents/food-delivery/food-delivery-app`.
- Verified local remotes, branches, commits, and dirty worktrees for six repositories. Cloned the missing documentation repository at commit `8de2f251eba2e9c67237ac8d6659b5a6e6cc7cf2`.
- Read both historical PDFs completely through extracted text and reviewed all 16 SVG source label sets and viewboxes.
- GitHub app access is currently unavailable; no remote mutations were made.
- Baseline passed: backend `go test ./...`, backend `go vet ./...`, frontend `npm run lint`, frontend `npm test -- --watch=false`, frontend `npm run build`, Terraform `fmt -check`, `init -backend=false -input=false`, and `validate`.
- Observed main gap: only users/common backend routes are registered; restaurant/cart/payment/notification/delivery code and order-service gRPC implementation are scaffolds.
- Preserved existing user changes in backend and frontend; no application files were edited.
- Checkpoint: audit state written to `project/` in this local documentation checkout. It is not synchronized remotely because GitHub push permission is unavailable.

## 2026-09-08 — VERT-001 implementation checkpoint

- Added migration `000023_add_catalog_fields`, public restaurant discovery/detail/menu routes, PostGIS radius filtering, bounded cursor pagination, and INR minor-unit menu output.
- Added deterministic fictional Shamgarh seed data for five restaurants, five owners, and six menu items with `make seed-demo`; two consecutive runs remained idempotent.
- Added focused cursor, bounds, and malformed-request tests. `go test -race ./...`, `go vet ./...`, and `git diff --check` pass.
- Rebuilt and recreated the local backend container; migration reached version 23. Live calls to discovery and menu returned five restaurants and the seeded menu. Validation with latitude 99 returned HTTP 400.
- Backend changes are on local branch `codex/vert-001-catalog`. Pre-existing dirty files remain unstaged. Independent staff review is active; owner mutations, cache, frontend, and downstream ordering are not part of this checkpoint.
- Commit checkpoint: `21db2b5a6aa95bf416f4e6260d1f67f522b972ab` (`feat: add restaurant discovery and seeded catalog`).
- Staff-review checkpoint: independent reviewer returned no packet within the bounded wait and was shut down; VERT-001 remains at staff-review evidence pending and is not claimed Done.

## 2026-09-08 — GitHub CLI access verified

- `gh auth status` verified the active account as `RishabhM2506` with `gist`, `read:org`, and `repo` scopes.
- `gh api user/orgs` verified organisation `SwaadFoodDelivery`.
- `gh repo list` verified the six core repositories with `ADMIN` permission: backend, frontend, infra, documentation, proto, and order-service.
- The in-product GitHub connector still reports no installation, so authenticated remote work will use `gh` unless the connector is separately authorized.

## 2026-09-08 — VERT-002 cart checkpoint

- Wired authenticated cart routes with HMAC-signed opaque tokens, user-scoped persistence, restaurant binding, item availability checks, customisations, and server-side INR minor-unit subtotal calculation.
- Added atomic Postgres add/delete/clear operations and unit/handler/HMAC tests. Fixed nullable empty-cart reads by separating cart metadata and item queries.
- Rebuilt the local backend image and ran an authenticated live flow: add/read subtotal `43800`, forged token `401`, restaurant mismatch `409`, clear `200`, empty read with zero items, and cross-user read `401`.
- `go test -race ./...`, `go vet ./...`, and `git diff --check` pass. VERT-002 remains review-pending and is not claimed Done.

## 2026-09-08 — review fixes and ORDER-001 checkpoint

- Independent review found P1/P2 issues: nonexistent menus returned 200, non-finite discovery coordinates were accepted, expired carts could mutate, owner menu mutation was absent, and cart conversion could break idempotent order replay. Fixed all implementation findings in backend commit `72e003e05089eadb5472f148565cb459a5678db7`.
- Added owner-scoped menu-item create/update/soft-delete routes under `JWTAuth + restaurant_owner`, database owner/path checks, and transactional audit rows. Live positive smoke test returned success for all three mutations and recorded three audit rows; non-owner access returned 404.
- Added ORDER-001 quote/place routes with address ownership/serviceability, server-side menu price checks, INR minor-unit totals, order-item snapshots, cart conversion, and UUID idempotency replay. Live authenticated flow passed quote, placement, and replay with the same order ID; the replay did not read the converted cart.
- Migration `000024` adds order instructions. A partitioned-table unique-index attempt was removed after live PostgreSQL rejected it; transaction-scoped advisory locking plus lookup now provides the idempotency guard. Database was repaired from dirty version 24 and is now version 24 clean.
- Final checks: `go test ./...`, `go test -race ./...`, `go vet ./...`, `git diff --check`, Docker build/start, migration guard, and health endpoint pass. Pre-existing `.env.development`, CI, README, Postman, and `.DS_Store` changes remain unstaged.

## 2026-09-08 — final independent approval

- Independent reviewer approved backend HEAD `7f3073036bb16114a5a0a3476bde619fa84a650a` after verifying the final lock ordering, stale-restaurant fix, expiry handling, radius/DB error classification, arbitrary idempotency keys, and customisation persistence.
- No P1/P2 implementation blockers remain. A dedicated concurrent SQL integration test remains desirable coverage, but is not a release blocker for this local checkpoint.

## 2026-09-08 — synchronization and demo delivery-partner checkpoint

- Used authenticated `gh` to push backend branch `codex/vert-001-catalog` and open [SwaadFoodDelivery/food-delivery-backend#11](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/11). The branch contains backend commits through `85b55011d87b13801c808045b040f7113fd6d040`; Go formatting, vet, tests, and Docker build passed in GitHub Actions.
- Added two fictional, repeatable Shamgarh driver fixtures to `scripts/seed_demo.sql`: `Swaad Demo Driver 1` and `Swaad Demo Driver 2`. Their document bytes are placeholders for local development only.
- Pushed the documentation checkpoint on `codex/docs-checkpoint` and opened [SwaadFoodDelivery/food-delivery-documentations#1](https://github.com/SwaadFoodDelivery/food-delivery-documentations/pull/1).
- The GitHub connector can read the backend repository, but `gh` remains the verified path for writes. No browser, supported scheduler, or quota telemetry capability is exposed in this session.

## 2026-09-08 — mock delivery simulation checkpoint

- Replaced the real-provider release blocker with an explicit demonstration boundary: `DELIVERY_PROVIDER=mock` and `MOCK_DELIVERY_DURATION_SECONDS=600`.
- Added migration `000026` for provider identity, persisted next-transition timestamps, and status-update actors. Order placement assigns an available fictional driver; a backend worker advances `assigned → en_route_to_restaurant → arrived_at_restaurant → picked_up → out_for_delivery → delivered`.
- Added `GET /api/v1/orders/:orderId/delivery`, duplicate-safe assignment, due-time catch-up after restart, cancellation/rejection guards, and a visible simulated-delivery label.
- Live Docker validation applied migration 26 and advanced a persisted due delivery to `delivered` with `Swaad Demo Driver 1`. `go test -race ./...`, `go vet ./...`, and `git diff --check` passed.
- Real courier credentials and onboarding are no longer required for this learning/demo scope. Browser validation, reachable deployment, scheduler capability, and quota visibility remain the only recorded setup limitations.

## 2026-09-09 — frontend mock tracking checkpoint

- Added frontend route `/orders/:orderId/tracking` and `deliveryService.getDeliveryStatus`, using authenticated polling against `GET /api/v1/orders/:orderId/delivery`.
- The view renders the simulated delivery label, fictional partner contact, and the six backend states; polling stops after `delivered`.
- Frontend PR [#3](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/3) contains commits `3c7cb9c` and `f264472`; GitHub Actions build, lint, unit-tests, and production-audit all pass. The lockfile refresh removed the production `nanoid` audit finding.
- Existing frontend user edits in `.github/workflows/frontend-ci.yml` and `README.md` remain unstaged. Browser deployment/persona validation, scheduler capability, and quota visibility remain the only recorded setup limitations.

## 2026-09-09 — mock payment checkpoint

- Implemented `ORDER-002` on backend commit `df1a960`: migration 27, `PAYMENT_PROVIDER=mock`, authenticated idempotent payment route, persisted success/decline outcomes, and deterministic `mock_fail`/`decline` sentinels.
- Added user-ownership verification before idempotency replay lookup, rejected payment for cash-on-delivery/cancelled/rejected/delivered orders, and made unsupported real mode fail clearly at startup.
- Backend `go test ./...`, `go test -race ./...`, `go vet ./...`, `git diff --check`, Docker build, migration application, health, route registration, and GitHub Actions all passed.
- The next ready implementation slice is the frontend customer discovery → menu → cart → checkout UI against the existing backend APIs. Browser deployment/persona validation, scheduler capability, and quota visibility remain setup limitations only.

## 2026-09-09 — customer vertical and restaurant owner API checkpoint

- Frontend commit `f1e4178` completed the source-level customer discovery → menu → cart → checkout → mock payment → tracking flow. It uses seeded Shamgarh coordinates, server-side cart/quote/order/payment APIs, address creation, and hands successful orders to tracking.
- Frontend lint, 49 unit tests, production build, and production dependency audit passed in GitHub Actions. Build warnings remain limited to existing Sass/PostCSS compatibility and vendor bundle-size advisories.
- Backend commit `684d50a` added owner-scoped restaurant order list/status routes with transition validation, `updated_by`, order history trigger compatibility, and audit rows. Full race tests, vet, Docker build, migration 27 state, health, route registration, and GitHub Actions passed.
- Phase 3 is complete for source/CI scope. Active ticket is now `PERSONA-001`; the next implementation slice is the restaurant-owner order queue UI, followed by driver availability/assignment/pickup and operations views. Browser deployment/persona validation, scheduler capability, and quota visibility remain setup limitations only.

## 2026-09-09 — restaurant-owner order queue checkpoint

- Added protected backend `GET /api/v1/owner/restaurant` so an authenticated owner resolves the active restaurant without frontend hardcoded seed IDs. Backend commit `82a25b6` is pushed to PR #11.
- Added frontend `/restaurant/orders`, role routing for `restaurant_owner`, owner queue loading, status chips, and valid accept/reject/prepare/ready actions. Frontend commit `4da4d2d` is pushed to PR #3.
- Backend `go test -race ./...`, `go vet ./...`, and `git diff --check` pass; Docker rebuilt and live registration shows the owner lookup route, with `/api/v1/health` returning 200 and unauthenticated owner lookup returning 401.
- Frontend lint, 51 unit tests, and production build pass. Existing Sass/PostCSS and vendor-size build warnings remain nonblocking.
- The next implementation slice is driver availability/assignment/pickup against the mock provider, then operations visibility. BLOCKER-003 remains limited to browser persona evidence, scheduler capability, and quota telemetry; real delivery remains intentionally de-scoped.

## 2026-09-09 — mock driver workflow checkpoint

- Added driver-only `GET /api/v1/driver/delivery` and `PATCH /api/v1/driver/delivery/status`; valid sequential actions update delivery/order actors and let the existing mock worker retain due-time fallback. Busy fictional drivers are excluded from new assignments. Backend commit `b658177` is pushed to PR #11.
- Added frontend `/driver` with role guard, availability/city controls, active-assignment empty state, and one-step progression actions. Frontend commit `ea7770f` is pushed to PR #3.
- Backend race tests, vet, diff checks, Docker build/start, health, and route registration pass. Frontend lint, 53 unit tests, and production build pass; existing Sass/PostCSS/vendor-size warnings remain nonblocking.
- The next implementation slice is operations visibility. Real delivery remains intentionally de-scoped. BLOCKER-003 still affects only browser persona evidence, scheduler capability, and quota telemetry.
