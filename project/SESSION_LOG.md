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
