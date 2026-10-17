# Delivery roadmap

## October 22 demo

- [x] Next.js application and explicit unconfigured state.
- [x] PostgreSQL schema, relations, exclusion constraints and guarded CRUD.
- [x] Customer/staff sign-in integration and role-gated pages implemented.
- [x] Reservation interfaces and basic admin activity viewer implemented.
- [x] Unit and isolated PostgreSQL/WASM rule tests.
- [ ] Configure an isolated Supabase development project; never reuse an unrelated production project.
- [ ] Verify actual hosted Auth, cookie refresh, direct API/RPC authorization and browser booking flows.
- [ ] Run two-session race test on native PostgreSQL (CI includes a disposable PostgreSQL service).
- [ ] Configure Vercel Preview and callback allowlist; verify deployed journeys.
- [ ] Capture complete Activity 6 evidence; then tag demo release.

## November launch

Three developers: customer UI; server/database/Auth; admin/reports/integrations/deployment.
Two QA: functional/usability/evidence; authorization/concurrency/financial/recovery.

- Oct 23–Nov 1: dining lifecycle, inventory management, customer cancellation policy, staff filtering/pagination.
- Nov 2–8: verified payment/refund ledger, account-linked purchase history, admin-generated employee transaction reports and CSVs, basic orders/menu, notifications/outbox and reminders.
- Nov 9–15: customer/staff usability, mobile/accessibility, authorization and load tests.
- Nov 16–20: restore rehearsal, policy confirmation, production configuration and staff training.
- Nov 23–29: supervised live Vercel pilot and actual-use evidence.

Customer purchase history must use account ownership, not an email match. Financial reports count each verified ledger entry once and retain original/correcting employees. The current activity viewer is not a financial report or complete POS.
