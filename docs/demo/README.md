# October 22 demonstration

1. Confirm an isolated configured Supabase project and the tested build SHA.
2. Sign in as customer A; load rooms from PostgreSQL.
3. Create a draft, reload, edit dates/guests, reload and physically delete the draft.
4. Recreate and submit a request. Verify a conflicting submission fails without partial writes; adjacent stays succeed.
5. Sign in as customer B; attempt A's detail URL and direct mutation. Both must deny access.
6. Staff confirms/checks in/completes; customer cannot invoke staff transitions.
7. Admin views employee-attributed audit events, including deletion history. Non-admin access must fail.
8. Test invalid dates, too many guests, client-supplied rates/roles, stale versions and repeat submissions.
9. Show independent database queries, SQL function/policy source and real test evidence.
10. Show Preview callback/login/logout and session switching in two independent browser contexts.

Do not submit customer credentials, secrets or actual customer personal data as evidence.
