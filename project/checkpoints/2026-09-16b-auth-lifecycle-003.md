# AUTH-LIFECYCLE-003 implemented — 2026-09-16 (Claude Code session, continued)

Supersedes only the "next exact action" of
[the earlier 2026-09-16 checkpoint](2026-09-16-claude-audit-and-lint-fix.md);
that one's audit findings (branch-protection blocker, PR10 lint fix, security
finding) still stand and are not repeated here. This entry covers new
engineering work done after the user chose to self-approve PRs on GitHub and
asked to keep implementing regardless.

## What was built

Per `BACKLOG.md`'s AUTH-LIFECYCLE-003 row (logout/session renewal/expired-
session browser recovery), an Explore pass first confirmed: logout was
already fully implemented on both sides (nothing to build); a refresh token
was minted and delivered on every login but **nothing ever consumed it** --
an access token's 60-minute TTL was unconditionally terminal; and an expired
session outside a route navigation left the UI looking signed in until the
user's next click. All three gaps are now closed:

- **Backend** — [PR22](https://github.com/SwaadFoodDelivery/food-delivery-backend/pull/22),
  branch `codex/auth-refresh`, stacked on `codex/order-identity-guard`
  (PR21 tip `776638c`), worktree `.worktrees/auth-lifecycle-backend`. Adds
  `POST /api/v1/auth/refresh`: parses the refresh JWT (new
  `utils.ParseRefreshToken`), confirms the paired session is still active in
  Redis via new `repository.GetSession`/`TouchSession` (the same liveness
  check `JWTAuthMiddleware` already trusts for access tokens), mints a fresh
  access token, and slides the session TTL forward. The refresh token is not
  rotated -- revocation rides entirely on the shared session (`jti`), which
  keeps this simple while still closing the same moment logout already
  closes. Sits in `v1Public` outside the guest-token-gated `/auth` group
  deliberately (a lapsed 60-minute guest token shouldn't be able to fail an
  otherwise-valid 30-day refresh). Verified: `go build ./...`, `go vet ./...`,
  and `go test -race ./...` all green across the full repo (not just the new
  package). New tests: `pkg/utils/jwt_test.go` (round trip/expiry/wrong-secret/
  cross-type-parse), `internal/services/users/business/auth_refresh_test.go`
  (fake-repository table test covering missing/malformed/expired token,
  session not-found/revoked/cross-user-mismatch, repo failures, and the
  success path). No Redis-backed integration test infra exists in this repo
  (confirmed by the Explore pass and by the PR body) -- building one was
  judged disproportionate scope for this ticket and is flagged, not silently
  skipped.
- **Frontend** — [PR11](https://github.com/SwaadFoodDelivery/food-delivery-frontend/pull/11),
  branch `codex/auth-session-recovery`, stacked on `codex/order-history-pagination`
  (PR10 tip `248017a`, the lint fix from the earlier checkpoint), worktree
  `.worktrees/auth-lifecycle-frontend`. `services/api.js`'s `request()` now
  tries one silent renewal (`hooks.refreshSession`) on a bearer-call 401
  before falling back to `onSessionExpired`, mirroring the existing
  guest-token-retry pattern exactly (retry exactly once, guarded by
  `retryOnSessionExpiry`, never on `/auth/logout` or a non-bearer call).
  `stores/auth.js` adds `renewSession()` (deduped in-flight promise, same
  shape as `refreshGuestSession`) and a `sessionExpiredSignal` counter bumped
  only on *terminal* expiry (renewal attempted and failed). `App.vue` gained
  a small `<script setup>` block watching that signal to force an immediate
  redirect to `/login` when the current route `requiresAuth` -- this is
  additive to the existing router-guard redirect-on-navigation, not a
  replacement for it. Verified: `npx eslint` clean on every changed file,
  `npx vue-cli-service test:unit` full suite 159/159 passing (151 pre-existing
  + 8 new: 3 in `api.spec.js`, 5 in `auth.spec.js`), `npx vue-cli-service build`
  clean. No new E2E spec added -- the full contract is already exercised at
  the request/store layer where the logic actually lives; disclosed as a gap
  in the PR body rather than left unstated.
- Both worktrees needed `git fetch`/`git worktree add` from scratch (this was
  the first Claude Code session working these repos, not a Codex-owned
  worktree); the frontend worktree's `node_modules` was symlinked from the
  sibling `serviceability-policy-frontend-fresh` worktree after confirming
  identical `package-lock.json` content, rather than re-running a slow
  `npm install` -- safe because worktrees share no other state and this one
  is gitignored either way.

## CI trigger bug found and fixed (affects every future stacked PR, not just this one)

Both new PRs initially showed **zero CI checks at all** -- not failing, just
absent (`gh run list` returned nothing). Root cause: `pull_request.branches`
in both `ci.yml` and `frontend-ci.yml` is a manually-maintained allowlist of
base branch names, one entry added per stacked PR, and the two newest stack
tips (`codex/order-identity-guard`, `codex/order-history-pagination`) were
never added -- so any PR based on either of them (including these two) got no
CI signal whatsoever. This is a structural bug that would have silently
recurred on every subsequent stacked PR. Fixed on both new branches
(`89cc17f` backend, `eeb6aed` frontend) by replacing the fixed list with
`[main, "codex/**"]`, so this class of bug cannot recur. `actionlint` passed
on both files before pushing. Confirmed working: both PRs picked up real CI
runs immediately after the push (`gh run list` showed `in_progress` within
seconds). Note: the main checkouts of both repos independently have
unrelated, unrecognized uncommitted WIP in these same two files (pinned
Action SHAs, a Railway deploy job, a Vercel deploy job, concurrency-group
changes) -- that WIP was left untouched; the branch-glob fix went into the
new worktrees/branches only, which never had that WIP in their history.

## Evidence

- Backend: `go build ./...`, `go vet ./...`, `go test -race ./...` all pass at
  commit `6983be3` (full repo, not just new files).
- Frontend: lint clean, 159/159 unit tests pass, production build clean, at
  commit `6dbe5f8`.
- CI status for both new PRs (backend #22, frontend #11) was still running
  when this checkpoint was written -- verify with `gh pr checks 22 --repo
  SwaadFoodDelivery/food-delivery-backend` and `gh pr checks 11 --repo
  SwaadFoodDelivery/food-delivery-frontend` before treating either as a
  passed gate. Neither has had independent Staff/QA review yet.

## Next exact action

1. Confirm both new PRs' CI actually went green in GitHub Actions (local
   results above are not a substitute -- this repo's own history has
   diverged local/CI behavior before, e.g. Node version differences).
2. These two PRs extend the same stack the earlier checkpoint already
   flagged as blocked on branch-protection approval -- see
   [the branch-protection blocker note](2026-09-16-claude-audit-and-lint-fix.md).
   The user said they will self-approve; nothing further to do here until
   that happens except continuing to implement.
3. Independent review (Staff/QA-equivalent pass) of PR22/PR11 has not been
   performed in this session -- only automated build/lint/test verification.
   Treat as implemented-and-verified-by-tooling, not yet reviewed, when
   deciding what's safe to merge first.
4. Next ready backlog item after this: re-check `BACKLOG.md` for what remains
   unstarted once the current stack's approval status is known -- do not
   assume the stack order above is still the deepest tip without checking
   `git log`/`gh pr list` fresh, since the user may have merged PRs between
   sessions.
