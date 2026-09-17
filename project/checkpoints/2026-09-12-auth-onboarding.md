# AUTH-BROWSER-002 — actual login and upload acceptance

Supersedes the current-state claims in the September 11 checkpoint; that file
remains the dependency/history reference. Original dirty user checkouts remain
untouched. Work is pushed to feature branches, not merged into main.

## Published feature

- Backend [PR17](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/17),
  `codex/mock-otp-outbox`, `214b4f6` (implementation `e7aa016`), based on PR16.
- Frontend [PR9](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/9),
  `codex/auth-onboarding-browser`, `ca48c0c` (implementation `f376818`), based on PR8.
- Both latest commits above only add reproducible setup/security documentation.
- Dependency stacks remain backend main → 11 → 12 → 13 → 14 → 15 → 16 → 17,
  frontend main → 3 → 4 → 5 → 6 → 7 → 8 → 9. Schema 29 is required; no new
  migration. Retarget/revalidate downstream PRs as parents merge, never bypass
  protected main or discard dependencies.

Backend adds an opt-in private mock SMS outbox restricted to development/test,
0700 directories and atomic 0600 JSON delivery files. Random OTP generation,
hashing/expiry, verification attempts, rate limits and actual session creation
are unchanged. There is no code-retrieval API, deterministic OTP or extra logging
when the outbox is enabled. Phone hashes remain sensitive correlation data.

Frontend adds document replacement for uploaded draft documents, preserving the
editor on failure. The new browser project seeds only fictional accounts and
profiles, not credentials or onboarding documents. Applicant and manager log in
through actual OTP endpoints. Sample PDF bytes are PUT to MinIO without bearer
headers, then confirmed by the owned authenticated storage callback. Browser
coverage includes wrong OTP rejection, HttpOnly refresh cookie, missing-document
submit guard, draft initialization, submit, reload, pending route denial, reject,
feedback, reopen, replace, resubmit, approve and driver access.

## Verification and gate

- Real auth/onboarding Chromium test passed twice: 1 passed in 8.4s each.
  Final implementation pair: backend `e7aa016`, frontend `f376818`.
- Frontend: 112 tests / 14 suites pass, lint and production build pass. Existing
  Sass/PostCSS warnings and approximately 1.6 MiB entrypoint remain unresolved
  performance work, not a claim of production performance acceptance.
- Backend: `go test -race ./...` and `go vet ./...` pass.
- Implementation CI: backend [34683579267](https://github.com/SwaadFoodDelivery/food-delivery-backend/actions/runs/34683579267)
  passes race/vet, real PostGIS integration/migrations and Docker build;
  frontend [34683628795](https://github.com/SwaadFoodDelivery/food-delivery-frontend/actions/runs/34683628795)
  passes mocked Chromium, unit tests, lint, production audit and build.
- Documentation-head CI and independent Staff/Architecture and QA/Product
  reviews are pending at this checkpoint. No formal GitHub approvals claimed.

## Resume environment

Authoritative checkouts remain `../.worktrees/serviceability-policy-backend` and
`../.worktrees/serviceability-policy-frontend-fresh`, on the branches above.
Fast frontend clone `/tmp/swaad-browser-remote-20260911`; Node22 executable path
`/opt/homebrew/opt/node@22/bin`. Reports under `node_modules/.cache/swaad-e2e/`
are replaced by subsequent runs. Auth trace/screenshots are disabled to avoid
persisting OTPs/tokens; only sanitized lifecycle evidence is attached.

Owned backend session at last check: 90976, port 18080, launched with
`MOCK_OTP_OUTBOX_DIR=/tmp/swaad-otp-outbox.U760tJ REDIS_DB=1 bash scripts/run-e2e-backend.sh`.
Check process/health before assuming it survives a new session. Disposable
PostGIS database `swaad_e2e_20260911`, dedicated Redis 16379, NATS 14222, local
MinIO 9000. User's ordinary dev backend 8080/data are unchanged.

```sh
E2E_LOCAL_SEED=1 E2E_BACKEND_URL=http://127.0.0.1:18080 E2E_PORT=4174 \
E2E_CLIENT_API_KEY=dev-client-api-key \
E2E_OTP_OUTBOX='/tmp/swaad-otp-outbox.U760tJ' npm run test:e2e:auth
```

This is a disposable local demo key, not a production secret. Normal OTP/IP
limits apply: each run sends two codes; four have been sent across the two live
runs. Rapid repeated runs can get 429; do not erase buckets or disable limits.
Legacy customer/persona fixture helpers use Redis DB 0, whereas the auth backend
currently uses DB 1. Restart only the owned backend with the matching DB before
running those legacy suites. Do not run against the ordinary development DB.

Successful auth consumes only its own matched outbox file. Interrupted runs may
leave private files: no automatic filesystem retention job exists. Do not print
or commit OTPs, tokens, identity fixtures or signed upload URLs. Stop the owned
process and inspect exact generated directories before any eventual cleanup.

## Remaining scope and next action

Finish independent review and documentation-head CI, address actionable findings,
update PR readiness and this checkpoint. Next bounded feature should cover the
remaining auth lifecycle (logout/session renewal/expired-session recovery), or
profile/address persona negatives from the backlog after inspecting existing
coverage. Registration/email verification, expiry/resend browser UX, real
providers, shared deployment and performance budgets are not validated by this
test. Actual app approval is not a GitHub review approval or real identity check.

Protected-main merge still requires a separate permitted reviewer. Development
can continue; no real provider credentials are needed. Quota visibility
unavailable; no quota percentage or automatic continuation/scheduler is claimed.
