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

## BLOCKER-004 — VERT-001 independent staff review not completed

- Status: open; affects VERT-001 Done gate.
- Evidence: a bounded staff-review subagent was started against commit `21db2b5a6aa95bf416f4e6260d1f67f522b972ab`, but returned no review packet before timeout and was shut down.
- Impact: implementation and focused QA evidence exist, but no independent approval or actionable review findings are available. The feature is not represented as fully Done.
- Next action: perform an independent review in a future run or have an authorized human review the commit, then rerun any affected checks after fixes.

## BLOCKER-005 — VERT-002 independent staff review not completed

- Status: open; affects the Done gate for the cart checkpoint.
- Evidence: cart implementation and focused QA are complete locally, but no independent review packet has been recorded.
- Impact: the cart slice is not represented as fully Done and the order slice should wait for review evidence.
- Next action: review the cart diff and record approval/findings, then rerun affected checks after any fixes.
