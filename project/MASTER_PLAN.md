# Swaad recovery and delivery plan

Status (2026-09-13): core local demo journeys have browser/CI evidence; auth and
first real order-service read PRs are ready but unmerged. This is not completion
of every persona, reliability, deployment or handover requirement.

## Evidence-based current state

The local workspace is macOS at `/Users/rishabhjain/Documents/food-delivery/food-delivery-app`.
Six repositories are available; authenticated GitHub CLI push/PR/CI access works.
The optional in-product connector is not required. Original dirty worktrees are
preserved and feature development uses isolated checkouts in `.worktrees`.

The Go/Gin backend uses Postgres, Redis and NATS. Customer checkout, mocked payment
and delivery, owner/driver/operations workspaces, in-app notifications and history
have implementation and local browser evidence. Actual OTP login, MinIO upload
and rejected-onboarding recovery are tested. The separate order-service now
implements an authenticated owned GetOrder RPC; all order writes still belong to
the backend. List/tracking/write extraction must not be reported complete from
that first read. Infrastructure code validation is not a verified deployment.

Current tested versions, reviews and limitations are in STATE.json and the
checkpoint it names. Earlier backlog/traceability rows are historical audit
snapshots, not reliable completion percentages. Remaining full-scope gates include
authentication/session negatives, profile/address acceptance, remaining persona
requirements, write-ownership integration, recovery/security/performance checks,
reproducible release and consolidated documentation. Real providers are explicitly
out of scope for the learning demo.

Agent Staff/Architecture/QA reviews supply technical evidence. They cannot satisfy
the separate author-independent GitHub approval rule using the PR author's account.
No branch protection changes are implied by autonomous development authorization.

## Delivery phases

1. Recovery foundation: preserve dirty work, keep the source inventory and evidence current, connect GitHub or use local issue fallback, and define the compatibility manifest.
2. Contract and foundation: reconcile the LLD/DB design with the actual migration and service boundaries; choose the simplest current architecture; establish API/proto/schema acceptance tests.
3. First vertical journey: customer discovery → menu → cart → quote/order → payment development adapter → restaurant update → backend-controlled mock delivery → customer tracking.
4. Persona completeness: customer addresses/history/notifications, restaurant operations, driver availability/assignment/earnings, and authorized operations tooling.
5. Reliability and integrations: cancellation/refund, idempotency, race handling, retries/recovery, uploads/notifications, and any approved real-provider adapters.
6. Experience and hardening: responsive accessible UI, reduced-motion 3D moments, performance budgets, security, observability, backups, and affordable load tests.
7. Development release and handover: integrated gates, persona evidence, reachable development deployment, walkthroughs, costs, limitations, and user-owned blockers.

## Architecture guardrails

- Preserve the existing Go/Vue/Terraform stack unless an ADR proves a change is necessary.
- Use backend-owned state machines and persisted due times for delivery simulation; a frontend timer is not sufficient.
- Keep mock providers explicit and fail clearly when a requested real provider is unimplemented.
- Use integer minor currency units for new order/payment contracts; document migration compatibility with existing decimal columns before changing them.
- Separate registered users, daily active users, peak concurrency, request rate, order rate, and tracking connections in all capacity claims.
- Do not add paid services, real payment/order flows, production deployment, or destructive migrations without explicit authorization.

## Gate sequence

Implementation → focused QA → independent staff review → persona acceptance. A changed commit invalidates evidence for the affected gate. Every handoff must name changed files, commit SHAs, commands/results, risks, and the next action.
