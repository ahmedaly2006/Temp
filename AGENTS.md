# Working on Vexoryna AI

## Scope and source of truth

These instructions cover this repository. Read `README.md`,
`PROJECT_STATUS.md`, and the relevant specifications in `docs/` before
changing behavior. The current repository is documentation and empty
implementation folders only. Do not report proposed features as working.
Preserve unrelated work and existing files; do not replace user-authored
content without authorization.

## Engineering boundaries

- Keep admin and worker POS experiences in `apps/web` and business rules in
  the eight modules under `apps/api/app/modules`.
- Put schema changes in `database/migrations` and tenant policies in
  `database/policies`. Never silently edit an already-applied migration.
- Use the existing modules for supporting concerns: suppliers/catalog in
  `products`, purchasing/documents in `invoices`, receipts and stock counts
  in `inventory`, cash expenses in `cash`. Avoid new modules unless needed
  and documented.
- Validate inputs on the server. Resolve tenant and branch access from the
  authenticated session, not from an untrusted submitted organization ID.
- Enforce roles and tenant isolation in the API and database. Test with two
  independent organizations and restricted branch users.
- Use exact money arithmetic, explicit currencies, UTC event timestamps,
  and branch-local business dates. Never use binary floating-point money.
- Post sales, deliveries, refunds, cash entries, and stock entries
  transactionally. Use persisted idempotency keys and concurrency controls.
- Preserve posted ledger history. Correct mistakes with linked reversal or
  adjustment entries and a reason, actor, and timestamp.
- AI extraction proposes data only. Approval must validate the latest
  reviewed invoice version and actual received units before posting stock.
- Keep originals in private storage. Never commit passwords, API keys,
  invoice uploads, production exports, or personal student data.

## Demonstration and UI

Use fictional records and the AIS non-affiliation notice. Seed exactly 250
unique products only when implementing the demo; preserve image licensing
and attribution, with clear product-specific fallbacks. Use independent
branch inventory. Generate development credentials securely rather than
hardcoding them. Label fixture extraction and simulated payments explicitly.
Do not claim offline support, payment settlement, or production readiness
without implementation and verification.

Keep the POS fast, touch-friendly, keyboard-accessible, and usable during
school breaks. Use English initially and prepare for Arabic/RTL. Reports
must derive from recorded transactions and distinguish revenue, gross
profit, expenses, and any net profit estimate.

## Validation and reporting

Place journey tests in `tests/e2e`, posting and concurrency tests in
`tests/integration`, isolation and authorization tests in `tests/security`,
and measured rush-hour scenarios in `tests/load`. Essential cases include
duplicate invoice approval, carton conversion, partial receipt, duplicate
sale retries, concurrent last-unit sales, cross-tenant denial, refunds, and
cash discrepancy calculations.

There are no test or startup commands yet. Add reproducible commands when
implementation begins; do not invent passing test results. Run checks
appropriate to a change and record outcomes and limitations. Update
`PROJECT_STATUS.md` when scope, implemented behavior, or decisions change.
Keep generated build products and credentials out of future commits.
