# Source inventory

Initial audit date: 2026-09-08 (Asia/Kolkata). Evidence is from local Git checkouts and the historical PDFs/SVGs listed below.

| Source | Local path | Commit / version | Review status | Evidence |
|---|---|---|---|---|
| Documentation repository | `food-delivery-documentations` | `aeaa1550e71ccbca7f694be34949ce800ca6cfac` (`main`) | Reviewed + current checkpoint | Both PDFs text-extracted page by page; all 16 SVG files inspected for source labels/dimensions; durable state updated |
| LLD | `FoodDelivery_LLD_Final _review.pdf` | v2.0, 33 pages | Reviewed | Sections 2–8; 49 API endpoint contract; pages 1–33 |
| DB design | `food_delivery_app_db_design_final_review .pdf` | v2.0, 1029 extracted lines | Reviewed | Sections 1–10; 29-table model, Redis, partitions, API mapping, DB security |
| Flowcharts | `flowcharts/*.svg` | 16 SVGs | Reviewed | Auth, onboarding, profile, address, discovery, menu, cart, place/cancel order, payment, assignment, tracking, notifications, uploads, driver, ER diagram |
| Main backend | `food-delivery-backend` | `72e003e05089eadb5472f148565cb459a5678db7` (`codex/vert-001-catalog`), base `766394291771ed5f6564d4f0cd35a7269b01d949` | Audited + catalog/cart/order/owner menu slices implemented; independent approval pending | README, Go module, router, config, migrations, service tree, baseline, catalog/cart/order/owner checks |
| Order service | `order-service` | `ce30cf2b07962e96447c330f650e96b8393a4771` (`main`) | Audited | Proto seam, gRPC registration, order business/repository stubs, module/config |
| Shared proto | `proto` | `c26b9ac16fbda449d38416ceb4aa75e00e59b10b` (`main`) | Audited | `order/order.proto` |
| Frontend | `food-delivery-frontend` | `8aea5fd13a335b1c347151f1394d8f65cab09cd5` (`main`) | Audited | README, package, route/view/service tree, baseline checks |
| Infrastructure | `food-delivery-infra` | `6b75b4f10fe20db9cf1fbb8376e09beef0991887` (`alignment_with_backend_and_frontend`) | Audited | README, Terraform root/modules, Railway config, baseline validation |

## Worktree conditions at audit

- Backend has user changes: modified `.env.development`, modified `.github/workflows/ci.yml`, and untracked `README.md`, Postman collection, and `.DS_Store` files. These are preserved and not claimed as orchestrator work.
- Frontend has user changes: modified `.github/workflows/frontend-ci.yml` and `README.md`. These are preserved.
- Order service, proto, infra, and fresh documentation checkout were clean at audit.

## Runtime/access evidence

- Host: macOS/Darwin arm64; Go 1.25.0, Node v25.9.0, npm 11.12.1, Docker 29.4.1, Terraform 1.15.8.
- Docker CLI and daemon are available; local Postgres/PostGIS, Redis, NATS, MinIO, migration guard, and backend are running. Migration 24 is clean.
- Mermaid CLI and Chromium binaries were not found; SVG source was inspected directly. Browser persona testing is not yet evidenced.
- In-product GitHub connector returned empty installations/accounts/orgs/repositories. Local `gh` CLI access is verified for `RishabhM2506` and organisation `SwaadFoodDelivery` with repository admin access; connector-only issue/project workflows remain unavailable.
- Quota visibility unavailable. No continuation scheduler was created; scheduler capability and local desktop availability remain unverified.
