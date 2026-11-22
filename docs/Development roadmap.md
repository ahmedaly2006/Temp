# Development roadmap

Status: starter plan, 2026-11-22. Milestones are ordered by dependencies,
not promised dates. Completion requires demonstrated behavior and recorded
evidence, not only generated screens or documentation.

## Milestones

| Milestone | Deliverable | Exit evidence |
| --- | --- | --- |
| Scaffold — complete | Requested folders and ten Markdown documents | Local structure/content inspection; empty folders retained with `.gitkeep` |
| M0 — Validate | Interviews with 5–10 operators, observed receipt/POS/close workflows, proposed pilot, confirmed MVP | Written workflow findings, agreed success metrics, and a prospective pilot partner |
| M1 — Foundation | Bootstrap web/API, local setup, Git/CI, schema, auth, organization/school membership, roles, private storage | Reproducible clean setup; four roles; tested cross-tenant and branch denial; versioned migrations/policies |
| M2 — Catalog and inventory | 250-product seed with images, two branches, suppliers, opening ledger, counts, wastage/expiry | Exact count/category/image validation; independent branch balances; ledger reconciles with expected examples |
| M3 — Purchasing and invoices | Uploads, structured/manual parsing, extraction adapter, review, pack conversion, goods receipts | 240-bottle conversion, partial receipts, duplicate/concurrent approval protection, failed-write rollback |
| M4 — Checkout | Fast POS, barcode/search, cash payments, receipts, stock deduction, approved refunds | End-to-end sale/refund evidence, duplicate retry protection, concurrent final-unit test, measured rush-hour usability |
| M5 — Reconciliation and reports | Drawer opening/closing, count approval, daily branch close, owner dashboard, exports | Worked cash example passes; source ledgers reconcile to reports; roles and dates filter exports correctly |
| M6 — Pilot readiness | Monitoring, support, backups/restore, onboarding, live integration and jurisdiction review as applicable | Demonstrated restore, recorded security/load results, agreed rollout/support plan and pilot acceptance |
| M7 — Pilot and iteration | Controlled school pilot and corrections based on observed use | Agreed pilot period completed with reconciled stock/cash, measured usability, and documented remaining issues |

M0 interviews can proceed while M1 foundations are developed. Keep one
complete delivery-to-sale-to-close journey working as features are added.
Do not replace persistence or permission checks with UI-only demonstrations
and then label the milestone complete.

## Suggested first implementation tasks

1. Confirm stack/provider choices and version pins; initialize the repository
   and create environment examples without secrets. Establish local startup,
   migration, seeding, and test commands in the README.
2. Implement organization, school, identity memberships, role policies, and
   a separate second test organization for isolation tests.
3. Add catalog/branch settings and reproducible, synthetic demo seed data.
   Verify all 250 SKUs, category counts, image sources/fallbacks, and opening
   movements before building reports.
4. Implement stock ledger and transactional posting rules; prove failure
   rollback, duplicate protection, and concurrent sale safety.
5. Add reviewed invoices and actual goods receipts; then checkout, closing,
   stock counts, and a reconciled owner report.

## Test organization

- `tests/e2e`: authorized admin and cashier journeys from receipt to close,
  including accessibility and branch switching.
- `tests/integration`: database transactions, unit conversions, cost/money
  rules, ledgers, idempotency, concurrency, and report reconciliation.
- `tests/security`: tenant/branch isolation, role permissions, protected
  files, session handling, untrusted invoice contents, and runtime policies.
- `tests/load`: documented peak checkout scenarios and measured throughput,
  latency, errors, and consistency; agree realistic loads with pilot staff.

## Deferred scope and open decisions

Defer voice, WhatsApp, advanced forecasting, international expansion, and
offline synchronization until the core workflow is reliable. Supplier price
comparison, reorder suggestions, billing integration, and richer analytics
can follow validated purchasing/stock history. Real electronic settlement
requires its own integration and reconciliation acceptance gates.

Resolve launch partner, image licensing, hosting, extraction provider,
Arabic invoice quality, cost method, tax configuration, retention/backup
objectives, and support ownership before the relevant milestone. Country
obligations must be assessed for the actual customer; no compliance is
established by these specifications.

Business/product ownership should cover interviews, requirements, pilot
coordination, and pricing. Technical/operations ownership should cover
architecture, implementation, verification, deployment, and support tooling.
Record actual owners and decisions as they are agreed. Keep
`PROJECT_STATUS.md` current with completed work, evidence, and next tasks.
