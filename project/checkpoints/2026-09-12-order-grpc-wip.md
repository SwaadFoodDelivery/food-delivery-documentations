# Continuation: ORDER-GRPC-001

Current feature: actual order-service integration, not full order write extraction.
Read the ADR `architecture/adr/2026-09-12-order-grpc-read-boundary.md`.

## Completed previous feature

Backend PR17 `214b4f6` and frontend PR9 `fd122c7` are ready for review and unmerged.
Actual auth/storage/review browser acceptance passes, including wrong OTP,
offline replacement failure and recovery. 118 unit tests pass, final frontend CI
34708366551 and backend CI34683839651 pass. Independent Staff/Architecture and
QA/Product agents resolved all current-scope blocking findings. The reporting
fix uses auth-specific counts-only reporting, private transient 0700 output and
preserveOutput never. Independent synthetic Chromium failure verified cleanup.
The earlier potentially credential-bearing HTML report was removed, not archived.
No human/formal GitHub approval or independent review of every older PR is claimed.

## New published / active work

- Proto [PR1](https://github.com/SwaadFoodDelivery/proto/pull/1): `f946f9d`,
  `codex/order-grpc-contract`, checkout `../.worktrees/order-grpc-proto`.
  Generated Go module, additive integer money fields, generation/wire tests.
  CI34708461039 passes. Consumers pin
  `v0.0.0-20260912173032-f946f9d3345e`.
- Backend [PR18](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/18):
  `31f210b`, `codex/order-grpc-read`, checkout `../.worktrees/order-grpc-backend`,
  based on PR17. Opt-in owned HTTP GET delegates actual typed gRPC. Full Go
  race/vet and real loopback HTTP→RPC contract tests pass. Draft; CI and independent
  review requested, paired real service/PostGIS acceptance pending.
- Order-service: `codex/order-grpc-foundation`, isolated checkout
  `../.worktrees/order-grpc-service`, based on `ce30cf2`. Implementation worker
  owns all files there. Real read-only repository, gRPC server/auth/config/startup
  and tests are in progress; no PR/commit claimed yet. Reconcile its Git state and
  worker handoff before editing. Original repo remains untouched.

Order-service origin still uses the old personal URL but GitHub redirects it to
SwaadFoodDelivery/order-service. There were no PRs because it was still a scaffold,
not because GitHub hid completed development. Root verified ADMIN visibility.

## Integration environment / next exact action

Root is provisioning only new disposable database `swaad_grpc_test_20260912` on
the existing local PostGIS server. Apply backend schema 29 and fictional demo
seed with `MIGRATION_TEST_DATABASE_URL` and `go test ./infra/postgres -run
'^TestMigrationsAndDemoSeed$' -count=1 -v`. Never use the ordinary dev database or
run the order-service's copied migration tree. Coordinate fixture ownership with
the order-service worker before its DB tests. Dedicated owned backend18 runtime
has not started yet; existing auth backend18080 and ordinary dev8080 are unchanged.

Next: finish/publish order-service PR; run independent review and paired actual
PostGIS/RPC/HTTP checks; fix findings; update all tested SHAs/CI/gates and walkthroughs.
Do not claim full PlaceOrder extraction: checkout, writes, payment/delivery and
migrations remain backend-owned. Order-service only adds owned reads in this slice.

Compiler tool install is isolated in `/tmp/swaad-protoc.4XaH99/bin`: official
protoc28.3, protoc-gen-go1.34.2, protoc-gen-go-grpc1.5.1. Generated sources are
committed; no tool/source files are stored in original dirty user checkouts.

Preserve auth final checkouts, original dirty worktrees and all stacked PRs.
Protected main still needs a separate permitted reviewer. No protection bypass,
real providers, paid resources, deployment, quota percentage or scheduler claim.
