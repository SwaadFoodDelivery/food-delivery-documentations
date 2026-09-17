# Provisional delivery forecast — 2026-09-13

This is a scope-based planning range, not measured velocity or a completion-date
guarantee. The current checkpoint is the source of verified implementation and
test evidence. Existing working features are not counted as missing because an
older walkthrough or initial audit row still describes them as pending.

## Milestones

- Consolidated usable local demo: 2–4 additional active days for fresh setup,
  a compatible release configuration and combined current-head browser acceptance.
  This is not complete order-service extraction or every original requirement.
- Full agreed mock/learning scope: 18–30 additional active days, including the
  local-demo consolidation above. At five productive days/week this is roughly
  4–6 calendar weeks; at three, roughly 6–10 weeks. Approval/access/quota waiting
  time is additional and currently unmeasured.

An active day assumes about 6–8 focused hours of implementation, tests, reviews
and fixes. Agent parallelism shares available usage; it does not imply unlimited
throughput. Re-estimate after order write-ownership design and two further
feature gates using recorded effort instead of this initial scope estimate.

| Remaining work | Active days | Acceptance outcome |
| --- | --- | --- |
| gRPC read completion | 2–3 | Owned paginated list, tracking/history contracts, consumers and paired checks |
| gRPC order maintenance | 6–9 | Place/cancel/status and payment/delivery ownership, preserving checkout atomicity and idempotency |
| Auth/security/recovery | 3–5 | Registration/mock verification, expiry/resend/logout/renewal, cross-role negatives and interruption recovery |
| Persona completion/polish | 2–4 | Mobile/keyboard/error states, accessibility/performance, close or explicitly defer unverified earnings/refund requirements |
| Operations/dev deployment | 3–5 | Reproducible authorized deployment, connectivity/security, restore/restart, health/logs and bounded load checks |
| Integrated release/documentation | 2–4 | Compatible-head regression, stacked-PR integration, current traceability/walkthroughs and verified setup/rollback |
| Total | 18–30 | Complete mock/demo scope, not a production scale guarantee |

## Assumptions and uncertainties

Providers remain explicit development mocks; no commercial onboarding, real
payment/courier service, paid infrastructure without approval, production launch
or million-concurrent-user claim. Keep Go/Vue/Terraform and the authoritative
database; do not introduce a major redesign. The largest uncertainty is moving
writes: current checkout spans cart conversion, order/item snapshots and
notifications while payments/delivery also change order state. Adding RPC stubs
does not complete that handover.

The latest whole customer/persona browser evidence predates the opt-in gRPC read
runtime; newer auth and paired gRPC gates cover their own scopes. Final release
must test a single compatible set rather than combining unrelated green checks
into a claim of full end-to-end coverage.

## Merge rule versus agent review

Independent Staff/Architecture/QA agents already review actual diffs and spec
acceptance. GitHub still requires an eligible approval from a separate authorized
identity on protected main. The connected author account cannot self-approve.
Agent role labels/comments do not alter this rule. Current order-service PR1 was
rechecked: CI passes, but reviewDecision=REVIEW_REQUIRED and mergeStateStatus=BLOCKED.
No protection changes or bypass are authorized by a request to use agents.

A separate permitted reviewer identity must approve under the existing policy.
Development on feature branches can continue while that one-time reviewer setup
is unresolved; its waiting time is not included in engineering-day estimates.
