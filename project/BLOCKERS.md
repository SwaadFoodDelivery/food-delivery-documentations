# Blockers and user-owned setup

## BLOCKER-001 — GitHub connector is not connected

- Status: open; affects ACCESS-001 and all remote issue/Project/PR synchronization.
- Evidence: GitHub connector returned empty `installations`, `accounts`, `orgs`, and `repositories` on 2026-09-08.
- Impact: pull/push permission, issue permission, Project permission, branch protection, and remote ticket duplication cannot be verified. No remote mutation was attempted.
- Can proceed: local audit, code changes, tests, local tickets/state, and draft patches.
- User action: connect/install the GitHub app for the relevant account or organization, then resume with the short prompt. Do not paste tokens into chat.

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
