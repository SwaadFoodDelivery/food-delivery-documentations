# Initial backlog

Stable local IDs are used until GitHub is connected. Each item is intentionally bounded to S/M/L and names its primary repository.

| ID | Phase | Priority | Size | Owner | Repositories | Status | Acceptance summary |
|---|---|---:|:---:|---|---|---|---|
| AUTH-001 | 2 | P1 | M | Backend | backend, frontend | Ready | OTP/session contract is integration-tested against explicit mock behavior and documents dev credentials/limitations |
| AUTH-002 | 2 | P1 | M | Backend | backend, frontend | Ready | Onboarding upload/submit/reject/resubmit paths have contract tests and safe mock storage callback behavior |
| CUST-001 | 3 | P1 | M | Backend | backend, frontend | Ready | Profile/address flows pass API and browser IDOR/soft-delete scenarios |
| VERT-001 | 3 | P1 | L | Backend | backend | Staff Review — evidence pending | Seeded Shamgarh restaurants are discoverable and menus are readable; owner menu mutation is scoped and audited |
| VERT-002 | 3 | P1 | L | Backend | backend | Focused QA passed — review pending | Cart token, restaurant binding, availability, persistence, and server-side subtotal work with negative tests |
| ORDER-001 | 3 | P1 | L | Backend | backend, order-service, proto | Blocked by VERT-002 | Quote/place/get/list/cancel use versioned contract, idempotency, server prices, and valid transitions |
| ORDER-002 | 3 | P1 | M | Backend | backend, order-service | Blocked by ORDER-001 | Explicit mock payment succeeds/fails; real mode fails clearly without an implemented adapter |
| DELIVERY-001 | 3 | P1 | L | Backend | backend, order-service, proto | Blocked by ORDER-001 | Persisted backend mock provider advances a successful order across documented states over 600s with restart/duplicate/cancel safety |
| FRONT-001 | 3 | P1 | L | Frontend | frontend, backend | Blocked by VERT-001/ORDER-001 | Customer discovery→tracking journey is responsive, accessible, and works with real local APIs |
| PERSONA-001 | 4 | P1 | L | Frontend/Backend | all app repos | Pending | Restaurant, driver, and operations journeys work against integrated services |
| DATA-001 | 2 | P1 | M | Architect/Backend | backend, order-service, proto, docs | Ready | Schema/contract differences from DB v2.0 are documented with compatible migration/rollback decisions |
| INFRA-001 | 2/7 | P1 | M | DevOps | infra, backend, frontend | Ready | Reproducible local/dev commands, health/log/backup/rollback evidence, and bounded cost estimate are recorded |
| ACCESS-001 | 1 | P0 | S | Orchestrator | GitHub/docs | Resolved via `gh`; connector pending | GitHub CLI access exposes the six repositories and confirms organisation/repository permissions; connector-specific installation is still unavailable |
| SCHED-001 | 1/7 | P2 | S | Orchestrator | docs | Blocked | Supported local scheduler is verified and an idempotent 4-hour task is created or documented as unavailable |
