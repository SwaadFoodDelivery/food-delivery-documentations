# Blockers and user-owned setup

## BLOCKER-001 — GitHub synchronization

- Status: resolved for the requested GitHub work.
- Evidence: `gh auth status` verified `RishabhM2506`; `gh repo view` verified admin access; authenticated `gh` pushed the backend branch and opened PR #11. The in-product GitHub fetch tool also reads the repository and reports push permission.
- Impact: `gh` supports the required branch, commit, PR, and CI workflow. Connector-only Project/issue workflows were not needed and remain unverified.
- Can proceed: authenticated `gh` synchronization, branch/PR operations, remote checks, and local tickets/state.
- User action: none for CLI work. Do not paste tokens into chat.

## BLOCKER-002 — Real delivery provider (de-scoped for demo)

- Status: closed for the demonstration/learning scope; real-provider integration is intentionally out of scope.
- Evidence: backend commit `7f1ddab` adds `DELIVERY_PROVIDER=mock`, persisted mock delivery timing, a restart-safe worker, seeded fictional drivers, and `GET /api/v1/orders/:orderId/delivery`.
- Impact: the app demonstrates the complete delivery journey without claiming live courier dispatch, GPS, or commercial delivery coverage.
- Future option: a real adapter can be added later behind the same provider boundary if the product scope changes.

## BLOCKER-003 — Browser/scheduler/quota capabilities are unverified

- Status: open; affects browser persona evidence and continuation scheduling only.
- Evidence: Chromium/Mermaid CLI binaries were not found; quota visibility unavailable; no scheduler capability was confirmed in this session.
- Impact: browser screenshots, visual persona acceptance, and automated four-hour continuation cannot be claimed yet.
- Can proceed: source-level work, API tests, documentation, and local builds.

## BLOCKER-004 — VERT-001 independent staff approval

- Status: resolved on 2026-09-08.
- Evidence: independent review found and the implementation fixed the nonexistent-menu 200 response, non-finite coordinate acceptance, and missing owner menu mutation. The owner mutation now has owner checks and audit rows. Independent approval after the fixes is still not recorded.
- Impact: no remaining review gate for the catalog slice.
- Resolution: independent reviewer approved final backend HEAD `7f3073036bb16114a5a0a3476bde619fa84a650a`.

## BLOCKER-005 — VERT-002 independent staff approval

- Status: resolved on 2026-09-08.
- Evidence: independent review found expired-cart mutation and order-replay risks; both were fixed and covered by the final code/tests/live flow. Independent approval after the fixes is still not recorded.
- Impact: no remaining review gate for the cart slice.
- Resolution: independent reviewer approved final backend HEAD `7f3073036bb16114a5a0a3476bde619fa84a650a`.
