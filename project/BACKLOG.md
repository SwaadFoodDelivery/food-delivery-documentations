# Initial backlog

Stable local IDs are used until GitHub is connected. Each item is intentionally bounded to S/M/L and names its primary repository.

| ID | Phase | Priority | Size | Owner | Repositories | Status | Acceptance summary |
|---|---|---:|:---:|---|---|---|---|
| AUTH-001 | 2 | P1 | M | Backend | backend, frontend | Ready | OTP/session contract is integration-tested against explicit mock behavior and documents dev credentials/limitations |
| AUTH-002 | 2 | P1 | M | Backend | backend, frontend | Ready | Onboarding upload/submit/reject/resubmit paths have contract tests and safe mock storage callback behavior |
| CUST-001 | 3 | P1 | M | Backend | backend, frontend | Ready | Profile/address flows pass API and browser IDOR/soft-delete scenarios |
| VERT-001 | 3 | P1 | L | Backend | backend | Done — independently approved | Seeded Shamgarh restaurants are discoverable and menus are readable; owner menu mutation is scoped and audited |
| VERT-002 | 3 | P1 | L | Backend | backend | Done — independently approved | Cart token, restaurant binding, availability, persistence, and server-side subtotal work with negative tests |
| ORDER-001 | 3 | P1 | L | Backend | backend, order-service, proto | Done locally — independently approved | Quote/place use server-side prices, address serviceability, idempotency, and converted-cart transition |
| ORDER-002 | 3 | P1 | M | Backend | backend | Done — demo mock provider | `PAYMENT_PROVIDER=mock` exposes idempotent order payment, deterministic `mock_fail` decline behavior, persisted outcomes, and explicit startup failure for unsupported real mode |
| DELIVERY-001 | 3 | P1 | L | Backend | backend | Implemented for demo — optional wall-clock run remains | Persisted backend mock provider advances a successful order across documented states over 600s with restart-safe due timestamps, duplicate-safe assignment, and cancellation guards |
| FRONT-001 | 3 | P1 | L | Frontend | frontend, backend | Done for demo — browser evidence remains | Customer discovery→menu→cart→checkout→mock payment→tracking UI works against the implemented APIs; visual/browser evidence remains unavailable |
| PERSONA-001 | 4 | P1 | L | Frontend/Backend | all app repos | In progress — operations workspace done | Customer, restaurant owner, driver, and operations demo journeys work against integrated mock APIs; browser persona evidence remains |
| DATA-001 | 2 | P1 | M | Architect/Backend | backend, order-service, proto, docs | Ready | Schema/contract differences from DB v2.0 are documented with compatible migration/rollback decisions |
| INFRA-001 | 2/7 | P1 | M | DevOps | infra, backend, frontend | Ready | Reproducible local/dev commands, health/log/backup/rollback evidence, and bounded cost estimate are recorded |
| ACCESS-001 | 1 | P0 | S | Orchestrator | GitHub/docs | Resolved via `gh`; connector pending | GitHub CLI access exposes the six repositories and confirms organisation/repository permissions; connector-specific installation is still unavailable |
| SCHED-001 | 1/7 | P2 | S | Orchestrator | docs | Blocked | Supported local scheduler is verified and an idempotent 4-hour task is created or documented as unavailable |

| OPS-001 | 5 | P1 | M | Backend/Frontend | backend, frontend, docs | Done for mock demo — browser evidence remains | Recipient-scoped notifications, order lifecycle events, customer history/cancellation, payment outcome notifications, and operations failure/stall counters are implemented |
| OPS-002 | 5/6 | P1 | M | Backend/Frontend | backend, frontend, docs | Done for mock demo — browser evidence remains | Restaurant-manager onboarding queue can list pending applicants, approve/reject with feedback, update access state, notify the applicant, and audit the decision |
| OPS-003 | 5/6 | P1 | S | Backend/Frontend | backend, frontend, docs | Done for mock demo — browser evidence remains | Restaurant-manager workspace can filter and inspect recent audit events, including before/after state payloads and bounded pagination |
