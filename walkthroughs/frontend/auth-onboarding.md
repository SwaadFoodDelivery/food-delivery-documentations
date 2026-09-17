# Browser authentication and onboarding recovery

Implemented in frontend PR9 with backend PR17 and their dependencies. The test
does not inject tokens: two new fictional accounts log in through actual OTP
endpoints. A private backend mock SMS outbox replaces transport only; normal
random generation, expiry, attempts, rate limits and session creation still run.

The driver applicant starts a draft, uploads sample PDF bytes directly to local
MinIO, then confirms the object with the authenticated backend. Submission waits
for all documents and leads to pending review, not application access. A manager
logs in separately, rejects with feedback, and later approves the resubmission.
The applicant replaces a document and gains driver access only after approval.
This application approval is not a human identity check or GitHub review.

`DocumentUploadCard.vue` emits replacement intent; `stores/onboarding.js` tracks
editing versus attempted upload state, and `OnboardingView.vue` gates submit.
An unstarted replacement can explicitly keep its current document. Once a PUT
has been attempted, failures require retry: storage may already have changed
even when confirmation failed. Both store and view prevent accidental submission
of a failed replacement. Current local selection state is discarded on reload;
persisted upload versioning is a separate scope.

`npm run test:e2e:auth` uses `playwright.auth.config.js`, not HTML-reporting browser
projects. Filled OTPs can appear in Playwright step titles even without tracing.
The dedicated reporter therefore emits counts only, suppresses error/title/log
content, disables trace/video/screenshots and uses private temporary artifact
directories with output discarded at normal completion. Interrupted runs may
leave private files; inspect exact generated paths before cleanup. Never publish
OTP outboxes, auth fixtures or override reporter safety settings.

Real browser acceptance includes incorrect OTP, HttpOnly refresh cookie, missing
document guard, pending route denial, actual storage PUT without bearer headers,
rejection feedback, explicit replacement discard, real offline PUT failure and
successful retry, new filename/editor closure, resubmission and approved access.
The final unit suite includes 118 tests; an independent synthetic Chromium
failure also verified credential artifact cleanup. Current CI/commit evidence
is in the release manifest/checkpoint.

Registration, email verification, expiry/resend UX, session renewal/logout and
shared deployment remain separate acceptance scopes. Payment/delivery/SMS/email
providers remain explicit mocks for this learning application.
