# SDD ledger — plan: docs/superpowers/plans/2026-10-17-cozy-palace-prototype.md
Pre-flight: Tasks 2→3→4→5 share RPC identity and ownership; use validated Supabase user context throughout. Task 6 consumes every earlier verification.
Ruling: Use the user-provided public repository and preserve its visibility — explicit repository choice supersedes proposed private repo — cost if wrong: source is public, no secrets will be committed.
Ruling: Fresh cloned repository on feat/nextjs-prototype is isolated; no nested worktree required — no existing local changes — cost if wrong: branch can be discarded without affecting main.
Ruling: Existing cloud Supabase project has no Cozy Palace association; do not modify it. Test with isolated local PostgreSQL if available — cost: hosted Auth/E2E may remain blocked until a development project is selected.
Ruling: Skill helper scripts unavailable at provider paths; retain task evidence directly in this ledger — cost: manual bookkeeping rather than helper automation.
Task 1: foundation config and smoke tests written; app baseline RED (missing app), pending UI implementation.
Task 2: SQL tests RED on absent tables/RPCs then GREEN; DATE assertion normalized as SQL text because driver returns Date. Expiry test RED then added transactional hold reconciliation with system-attributed audit events.
Ruling: PGlite runs actual PostgreSQL/WASM with btree_gist for isolated database rule tests; it is not hosted Supabase Auth nor independent concurrent sessions — cost: live Auth, transport and two-session race checks must run before demo readiness.
Task 3: cookie-aware Supabase clients, server identity/role gates and login/logout implemented. SQL ownership/admin checks pass; actual hosted Auth/cookie/browser identity verification remains blocked without a selected development project.
Task 4: validated Server Actions and server-only service/RPC boundaries implemented. Unit suite 18/18 and SQL suite 13/13 pass. Client authority fields rejected; errors mapped without exposing SQL.
Task 5: customer/staff/admin interfaces implemented. Public and mobile browser smoke tests 2/2 pass. Connected browser test supplied but not executed without development Auth configuration.
Task 6: CI, native two-session runner and evidence instructions supplied. Local build/lint/typecheck passed; native PostgreSQL concurrency, hosted Auth and Vercel Preview not executed. No demo-ready tag or production publication.
Ruling: OS package installation and Playwright CDN downloads failed in this runtime. Used npm-distributed Chromium 153 for browser checks via an optional executable-path override; no browser binary added to source — cost: CI still must verify its normal Playwright/browser pairing.
Final review: fresh reviewer /root/prototype_review; four Important findings and one minor.
Final: fixed PostgREST one-to-one shape — transport regression tests RED→GREEN; SQL suite remains green.
Final: fixed explicit expired/cancelled hold transition — regression RED→GREEN; SQL suite 15/15.
Final: fixed same-day already-expired holds — regression RED→GREEN; same-day arrival rejected consistently at create/update/submit.
Final: lock ordering changed to resources before reservations with post-lock version re-read; deterministic two-session regression added but cannot run locally. Native validation remains a stated gate, not a completion claim.
Ruling: Future-date-only arrivals for prototype — midnight hold model cannot safely represent same-day service — cost: same-day booking unavailable until an approved operational service-start policy is implemented.
Final: minor (deferred): parent-only audit snapshots omit old/new allocation dates; expand before operational audit acceptance.
Publishing blocked: GitHub create_branch returned 403 Resource not accessible by integration. Installation for neilfrfr uses selected repositories; user must grant the app access to this repository. No remote branch/ref/PR was created.
