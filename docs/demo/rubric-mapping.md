# Activity 6 mapping

| Requirement | Implementation | Evidence needed |
| --- | --- | --- |
| Database design | Versioned migration, FK/PK/check/exclusion constraints | Applied development schema and ERD |
| Connectivity | Server-only Supabase client and room/reservation queries | Configured app reads and independent query |
| CRUD | create/update/delete draft RPCs and authorized reads | Browser create/reload/edit/delete |
| Business logic | Transactional allocation, server prices, versions and idempotency | SQL suite plus native concurrent requests |
| Validation/security | Zod, database checks, RLS, role/owner gates | Invalid/cross-user/direct-call cases |
| Testing/debugging | Unit, PostgreSQL/WASM, native PG and browser suites | Actual logs and build SHA, not assumptions |
| Source submission | App Router actions, SQL, seeds and README | Reviewed feature branch and commit |
