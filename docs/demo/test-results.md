# Prototype verification record

Executed October 17 2026 against the prototype feature branch in the development workspace.

| Check | Actual result | Scope |
| --- | --- | --- |
| TypeScript | Pass | Full project typecheck |
| ESLint | Pass | Full project |
| Unit tests | 20 passed | Date/guest validation, authority injection, redirect safety, safe error mapping and PostgREST allocation normalization |
| PostgreSQL/WASM integration | 15 passed | Persisted CRUD, RLS/ownership, role/audit tampering, intervals, failed reschedule rollback, idempotency, versioning, active deletion, attribution, hold expiry, explicit expired/cancelled transitions and same-day policy |
| Next.js production build | Pass | Static and dynamic routes compile |
| Public browser tests | 2 passed | Desktop public navigation/setup and 360 px mobile overflow/navigation |
| Native PostgreSQL concurrency | Not run locally | Runner and fresh PostgreSQL CI service included; requires actual two-session execution |
| Hosted Supabase Auth/API | Not run | No development project selected or modified |
| Connected browser booking | Not run | Executable test provided; requires disposable customer account and configured Supabase |
| Vercel Preview | Not deployed | Requires isolated project configuration and callback allowlist |

The first browser installation through Playwright CDN failed in this runtime. Public tests ran with npm-distributed Chromium 153 through CHROMIUM_EXECUTABLE_PATH; CI uses the standard Playwright installation. A single-process browser attempt crashed on context reuse; normal multi-process execution passed both tests.

These results prove the stated local checks, not complete Activity 6 or live-release readiness. Follow the demo checklist before the presentation.

## Independent review and fix pass

The fresh reviewer found four Important issues. Allocation object normalization, explicit expired-hold transitions and rejecting same-day arrivals under the midnight hold model each have a regression test observed failing before the fix and passing afterward. Resource/reservation locking now follows one resource-first order and rechecks the reservation/version after acquisition. A deterministic two-session lock-inversion regression was added to the native PostgreSQL runner; it has not executed locally, so this concurrency fix is not claimed as live-verified.

Deferred minor: audit before/after JSON records the parent reservation but does not snapshot old/new room allocation dates. The activity log still records actor, action, time and affected reservation. Expand allocation snapshots before operational audit/report acceptance.
