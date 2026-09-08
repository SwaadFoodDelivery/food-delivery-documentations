# Blockers and user-owned setup

## BLOCKER-001 — GitHub connector is not connected

- Status: resolved for CLI-based synchronization; the in-product GitHub connector remains unavailable.
- Evidence: `gh auth status` and `gh api user` verified account `RishabhM2506`; `gh api user/orgs` returned `SwaadFoodDelivery`; `gh repo list` showed all six core repositories with `ADMIN` permission on 2026-09-08.
- Impact: GitHub CLI can be used for authenticated remote reads and writes. The connector-specific tools still return no installations/accounts/orgs, so connector-only Project/issue workflows remain unavailable.
- Can proceed: authenticated `gh` synchronization, branch/PR operations, remote checks, and local tickets/state.
- User action: none for CLI work. Do not paste tokens into chat.

## BLOCKER-002 — Real delivery provider is not selected/configured

- Status: open; affects DELIVERY-001 and release claims.
- Evidence: no delivery provider configuration or adapter was found in the backend; delivery package is scaffold-only.
- Impact: only an explicit backend mock adapter can be implemented locally. `real` mode must remain failing/blocked until a provider, credentials, commercial onboarding, and workflow are approved.
- Can proceed: mock-provider abstraction, persisted schedule, development controls, and tests.

## BLOCKER-003 — Browser/scheduler/quota capabilities are unverified

- Status: open; affects persona evidence and continuation scheduling.
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
