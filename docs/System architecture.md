# System architecture

Status: proposed architecture; no services or dependencies are installed.

## Components and trust boundaries

```text
Admin dashboard / Cashier POS (apps/web)
              | authenticated HTTPS requests
              v
FastAPI application (apps/api) -- authorization + domain transactions
      |                 |                    |
      v                 v                    v
PostgreSQL        Private object storage   Extraction worker/adapter
+ tenant policies invoice originals        structured proposals only
+ audit history   + product media          no stock-write authority
```

Proposed stack from the planning conversation: Next.js, React, TypeScript,
Tailwind and an accessible component library; FastAPI/Python; PostgreSQL;
Supabase Auth and storage where suitable. Select and pin supported versions
during bootstrap. Begin as one modular backend; a background worker can
share its deployment/repository rather than requiring many microservices.

## Directory ownership

| Location/module | Responsibility |
| --- | --- |
| `apps/web` | Admin and POS routes, accessible components, session UX, API client |
| `organizations` | Tenant lifecycle, membership, roles, subscription metadata |
| `schools` | Branch configuration, staff assignments, currency/time zone |
| `products` | Catalog, categories, suppliers, product images, branch pricing |
| `inventory` | Goods receipts, balances/ledger, transfers, lots, stock counts |
| `invoices` | Uploads, extraction jobs, purchase orders, invoice review/approval |
| `sales` | Checkout, receipts, payments, refunds, historical price/cost snapshots |
| `cash` | Drawers, sessions, cash movements, cash expenses, closing review |
| `reports` | Authorized summaries, reconciliation, exports |
| `database/migrations` | Versioned schema, indexes, constraints, functions |
| `database/policies` | Versioned tenant/branch policies with application order documented |

## Identity and isolation

Verify the authentication provider's tokens server-side, including issuer,
audience, expiration, and account state. Look up organization membership and
permitted branches before each operation. Never trust a branch selector or
submitted organization ID as proof of access.

Apply PostgreSQL row-level policies or equivalent tested database isolation
as defense in depth. If API connections use a shared database role, pass
validated tenant/user context within each transaction without leaking it
across pooled connections. Do not use an RLS-bypassing service role for
ordinary requests. Limit service credentials to audited administrative jobs;
never ship them to browsers. Scope object access to the same tenant rules.

## Posting and asynchronous work

A checkout validates price, quantity, available stock, role, and open session,
then commits sale items, payment records, cash effects, and inventory
movements in one database transaction. Scope persisted idempotency keys to
organization and operation; bind them to a payload hash so a key reused for
different content is rejected. Check concurrent balance/session updates
using locks or equivalent conditional writes.

Invoice extraction is asynchronous and retryable. It produces a versioned
proposal, not a posted receipt. Approval checks the reviewed version and
posts receipt/movements once. Use a transactional outbox or equivalent
durable scheduling for jobs that must follow a commit; retrying a worker
must not duplicate business events. Refresh dashboards from committed data.

For initial non-cash demonstration tenders, clearly label simulated payment
recording. Real payment providers require a separate payment-state workflow,
verified callbacks, provider idempotency, and reconciliation; a database
transaction cannot atomically settle an external payment.

## Files and AI integration

Validate file type, size, and parsing limits; authorize uploads/downloads;
use private storage and short-lived access URLs for invoices. Process
uploads as untrusted data, including text embedded in invoices. No document
instruction may grant tools, permissions, or posting authority. Send only
needed fields to a selected extraction provider after account/configuration
decisions are made. Validate structured output and retain corrections,
confidence/ambiguity information, and processing mode. No provider key or
live AI connection is needed for this scaffold.

## Operations and reporting

Separate development, staging, and production data, credentials, and storage.
Add environment configuration and local setup instructions during bootstrap.
Log correlation IDs and outcomes without secrets or unnecessary personal
data. Monitor failed jobs, denied access, posting errors, and slow checkout.
Plan database and document backups with demonstrated restore procedures.

Reporting reads committed records with explicit organization/branch/date
scope. Store UTC instants and derive local business dates using each
school's time zone. Support future currencies without summing unlike
currencies as one total. Distinguish purchases, cost of goods sold, revenue,
expenses, and profit estimates.

## Verification strategy

E2E: receive → sell → count → close → report. Integration: posting rollback,
duplicate requests, partial receipts, refunds, concurrent checkout, and
closing races. Security: role denial, cross-organization/branch access,
file access, and RLS through the real runtime database role. Load: measured
peak checkout mix with stated users, dataset, hardware, and latency/error
results. Build CI after the real application and test commands exist.
