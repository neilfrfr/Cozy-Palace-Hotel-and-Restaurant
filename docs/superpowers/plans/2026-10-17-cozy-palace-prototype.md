# Cozy Palace Next.js Prototype Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans for native execution, or superpowers:subagent-driven-development if the user selects that method. Execute each task through its test cycle and review gate.

**Goal:** Deliver the October 22 2026 Activity 6 prototype with authenticated, persistent hotel reservation CRUD, server validation, atomic conflict protection, employee-attributed audit events and reproducible evidence.

**Architecture:** Next.js App Router renders customer and staff pages on Vercel. Cookie-aware Supabase clients carry the validated user identity into PostgreSQL RPC transactions; database policies and constraints enforce ownership, prices and resource allocation. Keep integrations server-only and separate database tests from browser tests.

**Tech Stack:** Next.js, React, TypeScript, Tailwind CSS, Zod, Supabase Auth/PostgreSQL and @supabase/ssr, Vitest, Playwright, PostgreSQL integration tests, npm lockfile.

**Spec:** ../specs/2026-10-17-cozy-palace-nextjs-design.md

## Global Constraints

- Demo date: Thursday October 22 2026; live pilot: November 23 to 29 2026, Asia/Manila.
- Next.js build follows the current Next.js PRD tab, including activity logs and saved purchases.
- Keep Supabase for the first build. Neon was compared but no migration was selected.
- Use PHP and exact monetary arithmetic; store money as numeric(12,2).
- Demo CRUD uses one core hotel reservation module; schema includes dining resources.
- Drafts do not hold inventory; pending, confirmed and checked_in room allocations do.
- Never use an availability SELECT followed by an unprotected booking INSERT.
- Never expose service-role credentials or use browser-provided employee identities.
- No real payment gateway, production customer data, or commercial deployment in this first increment.
- Three development workstreams and two QA workstreams remain the human team structure.
- Proposed repository: private neilfrfr/cozy-palace. Check availability before creating; do not reuse or overwrite an unrelated repository.
- This planning phase writes no product code. User review of this plan precedes implementation under Superpowers.
- Select compatible current stable dependency releases at execution and commit exact resolved versions.

## Review Focus

1. Concurrent requests for one room: one active allocation succeeds and the other returns AVAILABILITY_CONFLICT.
2. Expired session or wrong owner: direct actions/RPCs fail even if an old page remains open.
3. Date boundaries: adjacent stays succeed; invalid and equal dates fail; draft edits still validate capacity.
4. Repeated submissions and stale edits: idempotent creation returns the original record; outdated versions do not overwrite newer work.
5. Audit tampering and customer identity: actor comes from the authenticated session, logs cannot be rewritten, and customers cannot claim another account's history.

## Increment boundaries

The first increment implements hotel reservations, customer/staff login, inventory reads, minimal customer/staff views and audit capture. A basic admin activity viewer can expose this audit data. Dining workflow, verified payment posting/refunds, generated transaction reports, customer purchase history, email reminders and full operations remain mandatory November work, not features to pretend are already implemented.

The first demo does not meet the final PRD by itself. Track unfinished launch requirements explicitly in docs/roadmap.md.

## File structure

- app/layout.tsx, app/globals.css: shared accessible shell and design tokens.
- app/page.tsx, app/rooms/page.tsx: public information and available inventory.
- app/(auth)/login/page.tsx, app/(auth)/login/actions.ts: authentication entry.
- app/(auth)/auth/confirm/route.ts: supported confirmation/recovery callback.
- app/(customer)/reservations/page.tsx and [id]/page.tsx: owned booking list/details.
- app/(staff)/staff/reservations/page.tsx: staff reservation management.
- app/(admin)/admin/activity/page.tsx: authorized employee activity viewer.
- app/actions/reservations.ts: thin Server Actions; no duplicated SQL rules.
- lib/supabase/client.ts, server.ts, session.ts: browser/server clients and verified identity.
- proxy.ts or the equivalent supported by the selected Next.js version: token refresh only; not sole authorization.
- lib/reservations/schema.ts, types.ts, service.ts: domain input, result contract and RPC adapter.
- lib/auth/authorize.ts: trusted role checks used by pages and actions.
- components/reservations/: accessible forms, status displays and action controls.
- supabase/migrations/: CLI-generated schema, policy and RPC migrations.
- supabase/seed.sql: fictional inventory, no real credentials.
- tests/unit/, tests/integration/, tests/e2e/: separate verification layers.
- docs/demo/, docs/roadmap.md, README.md: evidence, remaining scope and setup.

## Task 1 Repository and reproducible application foundation

**Files:** package.json, package-lock.json, tsconfig.json, next.config.ts, eslint.config.mjs, postcss.config.mjs, app/layout.tsx, app/globals.css, app/page.tsx, .gitignore, .env.example, .github/workflows/ci.yml, README.md.

**Interfaces:** Node 24 development runtime; npm scripts dev, build, lint, typecheck, test, test:integration and test:e2e. UI displays Cozy Palace Hotel and Restaurant.

- [ ] Confirm plan review and execution method; obtain browser fallback permission for GitHub creation.
- [ ] Check whether neilfrfr/cozy-palace exists. Create a private repository only if available; verify owner and returned URL.
- [ ] Initialize a fresh local checkout and commit the current spec and reviewed plan before product changes. Use a feature branch for prototype work. A fresh repository has no existing working changes to isolate.
- [ ] Add a failing smoke test that requests the homepage and expects a heading matching /Cozy Palace Hotel and Restaurant/.
- [ ] Scaffold App Router with TypeScript and Tailwind, lock dependencies and add the declared scripts.
- [ ] Add a clear setup page/state when Supabase configuration is missing; do not silently use fake persistence.
- [ ] Run npm run typecheck, npm run lint and npm run build; verify the smoke test after the browser harness is ready.
- [ ] Commit foundation and CI; keep .env files, tokens, build files and node_modules out of git.

## Task 2 Database contracts and conflict protection

**Files:** supabase/config.toml, CLI-generated migrations, supabase/seed.sql, lib/reservations/types.ts, tests/integration/reservations.test.ts, tests/integration/helpers.ts.

**Interfaces:**
- ReservationInput = { roomId: string; checkIn: string; checkOut: string; guests: number; idempotencyKey: string }.
- Reservation = { id: string; reference: string; status: ReservationStatus; version: number; total: string }.
- ReservationStatus = 'draft' | 'pending' | 'confirmed' | 'checked_in' | 'completed' | 'cancelled' | 'expired'.
- PostgreSQL create_reservation(room_id, check_in, check_out, guests, idempotency_key) returns an owned draft; submit_reservation(id, expected_version) acquires inventory atomically.
- update_reservation(id, expected_version, room_id, check_in, check_out, guests) and delete_draft(id, expected_version) preserve permissions and audit history.

- [ ] Use supabase --help and relevant subcommand help to select supported local setup/migration commands.
- [ ] Write failing real-database tests: create/read/update/delete own draft; reject invalid dates and excess guests; deny another customer.
- [ ] Write a concurrency test using two independent database sessions: concurrent submissions for one overlapping interval yield exactly one success. Also assert adjacent [start,end) stays succeed.
- [ ] Implement profiles, trusted staff_roles, room_types, rooms, restaurant_tables, reservations, room_bookings, table_bookings and audit_logs with PK/FK/check constraints.
- [ ] Store occupancy-blocking state beside the allocation and maintain it transactionally. Add resource/range exclusion constraints and serialize resource mutation where needed.
- [ ] Implement RPCs with bound parameters, server-calculated rates and ownership checks. Create/modify/submit/delete and audit insertion must commit or roll back together.
- [ ] Add idempotency uniqueness by owner/key; a repeated key with a different payload fails rather than silently returning an unrelated booking. Persist expected_version checks.
- [ ] Hard-delete only unpaid drafts and their allocation rows; retain an audit event containing a safe record identifier without an FK that forces audit deletion.
- [ ] Run npm run test:integration against a real isolated PostgreSQL/Supabase environment; never mark this passed against a mocked repository.
- [ ] Commit SQL, fixture and integration tests.

Example assertions to pin the interface:

```ts
expect(created.status).toBe("draft");
expect(reloaded.reference).toBe(created.reference);
expect(updated.version).toBe(created.version + 1);
expect(await lookupDeleted(created.id)).toBeNull();
expect(conflictingResults.filter(r => r.ok)).toHaveLength(1);
expect(adjacentResults.every(r => r.ok)).toBe(true);
```

## Task 3 Cookie authentication and access boundaries

**Files:** lib/supabase/client.ts, server.ts, session.ts, lib/auth/authorize.ts, proxy.ts or supported equivalent, app/(auth)/login/page.tsx, actions.ts, app/(auth)/auth/confirm/route.ts, tests/integration/authorization.test.ts, tests/e2e/auth.spec.ts.

**Interfaces:**
- requireIdentity(): Promise<{ userId: string }>.
- requireStaff(): Promise<{ userId: string; role: 'staff' | 'admin' }>.
- Request-scoped Supabase server client forwards the validated user session; role data comes from protected database records.
- createBrowserClient() is used only where an interactive browser session is required.

- [ ] Write failing tests for anonymous mutation, customer B reading A's record, customer role escalation and staff-only route access.
- [ ] Add a session-expiry test and a two-account browser test that never displays A's reservation content to B.
- [ ] Implement @supabase/ssr cookie handling using official guidance for the installed versions; validate claims or current Auth identity in server operations.
- [ ] Implement login/logout and callback handling; validate redirect destinations to avoid external redirect injection.
- [ ] Configure RLS for all exposed tables, including read and write checks. Do not grant customer writes to role, payment or audit authority.
- [ ] Run authorization and browser tests, including direct RPC calls; hidden navigation alone is not evidence.
- [ ] Commit auth, policies and tests.

## Task 4 Reservation services and validated Server Actions

**Files:** lib/reservations/schema.ts, service.ts, app/actions/reservations.ts, tests/unit/reservation-schema.test.ts, tests/integration/reservation-actions.test.ts.

**Interfaces:**
- parseReservationInput(value: unknown): ReservationInput, rejecting invalid input.
- ActionResult<T> = { ok: true; data: T } | { ok: false; code: string; fieldErrors?: Record<string,string[]> }.
- createReservationAction, updateReservationAction, deleteDraftAction and submitReservationAction consume untrusted form input and return ActionResult<Reservation> or ActionResult<{ id: string }> for deletion.

- [ ] Write a failing schema test asserting equal/reversed dates, fractional guest counts, invalid UUIDs and excessive string lengths are rejected.
- [ ] Write failing action tests for client-modified totals/owner/employee values, stale versions and conflicting allocation.
- [ ] Implement Zod parsing and server-only services; derive customer/employee identity from the session and monetary totals from database rates.
- [ ] Map database failures to stable safe error codes without exposing SQL/internal secrets.
- [ ] Revalidate the affected reservation/availability views after successful mutations. Avoid shared caching for private data and authoritative availability.
- [ ] Verify an action failure leaves the previous booking unchanged and a retry does not duplicate it.
- [ ] Run npm run test and npm run test:integration; commit.

Example schema assertions:

```ts
expect(schema.safeParse({ ...valid, checkOut: valid.checkIn }).success).toBe(false);
expect(schema.safeParse({ ...valid, guests: 1.5 }).success).toBe(false);
expect(schema.safeParse(valid).success).toBe(true);
```

## Task 5 Customer staff and activity interfaces

**Files:** app/rooms/page.tsx, app/(customer)/reservations/page.tsx, app/(customer)/reservations/[id]/page.tsx, app/(staff)/staff/reservations/page.tsx, app/(admin)/admin/activity/page.tsx, components/reservations/*, tests/e2e/reservations.spec.ts, tests/e2e/activity.spec.ts.

**Interfaces:** Server Components consume authorized service data; forms call Task 4 actions. Guest input survives validation/conflict errors. Audit viewer lists actor name/ID, action, timestamp and affected reference with pagination.

- [ ] Write a failing browser journey: login, create draft, reload, edit guest/date input, reload, delete draft, confirm disappearance.
- [ ] Write a second journey: submit a booking, attempt overlap, see an actionable conflict, and preserve entered details.
- [ ] Implement a warm, clear hotel interface with reusable navigation, labeled forms, loading/empty/error states and keyboard focus. Do not invent real room inventory or prices; identify fixture data as fictional.
- [ ] Add staff reservation management and the admin activity view. Enforce permissions in server reads/actions; customer/system events must not be misattributed to employees.
- [ ] Add a no-results/failed-fetch state and prevent repeated submits while pending. Server idempotency remains required.
- [ ] Run Playwright at 360 px mobile and 1366 px desktop. Verify dates, totals, status labels, keyboard use and no horizontal page scrolling.
- [ ] Commit interfaces and tests.

## Task 6 Deployment rehearsal and Activity 6 evidence

**Files:** .github/workflows/ci.yml, playwright.config.ts, docs/demo/README.md, docs/demo/test-results.md, docs/demo/rubric-mapping.md, docs/roadmap.md, README.md.

**Interfaces:** Reproducible setup and seed commands; a tagged demo build; test records state build SHA, environment, input, expected result, actual result and evidence path.

- [ ] Write the Activity 6 mapping for schema, connectivity, CRUD, server rules, validation/security, testing and submitted source.
- [ ] Run npm ci, npm run typecheck, npm run lint, npm run test, npm run test:integration, npm run build and npm run test:e2e. Record actual outcomes; do not silently skip unavailable infrastructure.
- [ ] Verify real data writes through an independent database query and show the room overlap race using two requests.
- [ ] Deploy only to an isolated Vercel Preview/test environment with approved test Supabase configuration; no production database reuse. Verify callbacks, recovery, cookies, direct routes and staff authorization on that URL.
- [ ] Run the Superpowers verification-before-completion and requesting-code-review workflows before claiming the increment complete. If native execution is selected, follow its required final review.
- [ ] Push the tested feature branch and create a reviewable draft PR; do not merge automatically.
- [ ] Tag the October 22 demo only after its required acceptance passes.
- [ ] Record launch backlog: dining lifecycle, posted payment/refund ledger, admin-generated employee transaction CSVs, account-linked purchase history, notices/reminders, reporting, recovery rehearsal and actual-user pilot.

## Team and dates

| Window | Developers | QA |
| --- | --- | --- |
| Oct 17–18 | Dev 1 customer shell; Dev 2 schema/auth; Dev 3 staff shell/CI | QA 1 rubric/flows; QA 2 role/concurrency cases |
| Oct 19–20 | Wire real reservation CRUD and SQL rules | QA 1 CRUD regression; QA 2 direct API/session/race tests |
| Oct 21 | Resolve blockers; freeze demo candidate | Rehearse, verify persisted changes and compile evidence |
| Oct 22 | Demonstrate backend source and workflow | Present results and record review findings |
| Oct 23–Nov 8 | Dining, payments, history, notifications, reports | Integration and employee attribution tests |
| Nov 9–20 | Address usability/security issues; production readiness | User tests, load checks, restore and release verification |
| Nov 23–29 | Supervised Vercel release and operator support | Record actual-use outcomes and retest fixes |

## Infrastructure dependencies and current state

- Connected GitHub account verified as neilfrfr; repository creation is unavailable through the currently exposed connector.
- GitHub CLI, Docker and psql were not found in this workspace during planning. Do not assume infrastructure is ready.
- Browser fallback for repo creation requires user approval because a connected plugin is present but lacks that operation.
- No Supabase project has been selected or modified. Use an isolated development project or a verified supported local runtime before claiming database integration.
- If infrastructure cannot be configured, implement reviewable code and tests but clearly report the blocked real-database checks. A mock demo is not Activity 6 completion.

## Plan review and execution handoff

Review this plan against the Next.js specification before implementation. Recommended method: native execution for this first tightly coupled slice, with the skill-required independent review at the end. Subagent-driven execution is available if explicitly selected. Repository visibility defaults to private; no member assignments are invented.

