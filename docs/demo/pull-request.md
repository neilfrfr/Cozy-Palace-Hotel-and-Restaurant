# Add the Next.js hotel reservation prototype

Customers can save and manage hotel reservation drafts through a Next.js App Router interface. Staff can progress bookings and administrators can inspect employee-attributed activity. Supabase/PostgreSQL source includes RLS, guarded RPCs, interval conflict constraints, server pricing, version checks, idempotency and transactional audit capture.

## Validation

- Typecheck, lint and production build pass locally.
- 20 unit tests, 15 PostgreSQL/WASM rule tests and 2 public/mobile browser checks pass.
- Independent review identified four Important issues; transport normalization and hold lifecycle regressions now pass. The resource-first lock-order fix has a deterministic native concurrency regression supplied, but native execution remains pending.

## Remaining setup and limits

- Configure an isolated Supabase development project and disposable customer/staff accounts.
- Run actual Auth/PostgREST/browser flows and native PostgreSQL concurrency checks.
- Configure Vercel Preview callbacks and verify the deployed flow.
- Prototype accepts future check-in dates only.
- Deferred minor: before/after audit JSON does not snapshot old/new allocation dates.
- Dining, payment/refund posting, saved purchase history, generated employee financial reports and reminders remain November launch work.

Keep this PR in draft until the infrastructure checks pass. Do not merge automatically.
