# Swaad recovery and delivery plan

Status: audit complete for the initial baseline; implementation planning is active.

## Evidence-based current state

The local workspace is a macOS Darwin arm64 machine at `/Users/rishabhjain/Documents/food-delivery/food-delivery-app`. Six repositories are now present locally, with the documentation repository freshly cloned because it was absent. The expected GitHub organization is not connected to the GitHub app in this session: the connector returned no installed accounts, installations, organizations, or repositories. Public Git fetch/clone works; push and issue/project permissions remain unverified.

The backend is a Go/Gin monolith with Postgres, Redis, NATS, and gRPC seams. User authentication, onboarding, profile, address, and upload routes are wired. Restaurant, cart, payment, notification, and delivery packages contain scaffolding but are not wired into the router. The separate order service and its gRPC registration/business/repository implementation are stubs. The frontend currently contains landing, auth, onboarding, and profile journeys. Infrastructure is Terraform-managed and validates locally, but cloud credentials and deployment reachability were not tested.

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
