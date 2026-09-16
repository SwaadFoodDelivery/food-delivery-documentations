# Initial backlog

Stable local IDs remain the planning reference; GitHub PRs are connected through
`gh`. The current checkpoint supersedes historical status wording in this table.

## 2026-09-11 acceptance updates

| ID | Outcome | PRs / next scope |
| --- | --- | --- |
| PAYMENT-RECOVERY | Implemented/tested; unmerged | Backend 13; frontend 7/8; payment gate, same-order retry and interrupted mock recovery |
| AUTH-SERIALIZATION | Implemented/PostGIS concurrency-tested; unmerged | Backend 14; stale applications cannot revoke newer approval |
| HISTORY-029 | Implemented/transaction-tested; unmerged | Backend 15; actual history emitters/current state |
| OPS-READ-REPAIR | Implemented/real-browser-tested; unmerged | Backend 16; overview and audit SQL repairs |
| PERSONA-001 | Local happy-path acceptance passed; unmerged | Frontend 8: owner, driver, manager, pending/approved applicant; customer cases also pass |
| AUTH-BROWSER-002 | Implemented; browser/CI/independent scope review pass; unmerged | Backend17; frontend9; actual OTP and MinIO, offline replacement recovery, secure reporter |
| ORDER-GRPC-001 | Implemented; paired/QA/Staff/CI pass; ready, unmerged | Proto1/backend18/order-service1; owned read, backend retains writes/migrations |
| ORDER-GRPC-002 | API implemented/reviewed/CI-green; ready, unmerged | Proto2 dc2e8c6 / service2 4d5fdd9 / backend19 ad30cf7; actual paired PostGIS pagination passed |
| ORDER-GRPC-002-UI | In progress, M | codex/order-history-pagination from frontend e660318; cursor append/retry/refresh, stale-response guards, unit/mocked and real browser checks |
| ORDER-IDENTITY-001 | P1 Changes required, M | [Backend issue20](https://github.com/SwaadFoodDelivery/food-delivery-backend/issues/20); separate codex/order-identity-guard worktree; reject owner-scoped ambiguous UUIDs before history/cancel and use composite UPDATE; prerequisite for final pagination UI action safety |
| AUTH-LIFECYCLE-003 | Implemented/unit-tested; unmerged | [Backend PR22](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/22) adds `POST /auth/refresh` (validates refresh JWT, checks paired Redis session liveness, mints new access token, no rotation -- revocation rides the shared session); [frontend PR11](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/11) retries once via silent renewal on a bearer 401 before falling back, and forces an immediate login redirect on terminal mid-use expiry (previously only caught on next navigation). Logout was already fully implemented pre-existing, both sides. No Redis integration-test infra or new E2E coverage added (disclosed gap, matching this ticket's proportionate scope); covered by fake-repository backend unit tests and request-layer frontend unit tests instead. |

## Historical feature inventory

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
