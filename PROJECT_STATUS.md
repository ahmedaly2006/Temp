# Vexoryna AI — Project status

Last updated: 2026-11-22

## Current milestone

Project scaffold and starter specifications. Product implementation has
not started. This file distinguishes completed preparation from planned
functionality; a documented requirement is not an implemented feature.

## Completed preparation

- Requested `apps`, `database`, `tests`, `docs`, and `.github/ISSUE_TEMPLATE`
  directory structure, with eight backend module folders.
- Six starter specification documents and four root Markdown files.
- `.gitkeep` files in all 16 otherwise-empty leaf folders.
- Initial scope, role boundaries, data model, invoice workflow, cash
  equations, and milestone acceptance criteria documented.

## Not implemented

| Area | State |
| --- | --- |
| Admin dashboard and worker POS | Planned |
| API and database migrations/policies | Planned |
| Authentication and four roles | Planned |
| 250 products, images, and branch seed data | Planned |
| Invoice extraction and approval | Planned |
| Inventory ledger and physical counts | Planned |
| Sales, refunds, and cash reconciliation | Planned |
| Reports and exports | Planned |
| Automated tests and CI | Planned |
| Git initialization, remote, hosting, and backups | Not configured |

## Working decisions

- Start with Egypt, EGP, English, and Arabic/RTL-ready design.
- Use fictional AIS-WEST and AIS-MAIN demonstration branch labels with a
  visible non-affiliation notice; do not imply verified campus coverage.
- Proposed stack: Next.js/TypeScript, FastAPI, PostgreSQL, and Supabase
  services where appropriate. Dependency versions remain unselected.
- Require human approval before receipt posting; use append-only stock and
  cash ledgers, exact money values, and idempotent transactions.
- Begin with online checkout. Offline synchronization, voice, WhatsApp,
  advanced forecasts, and international rollout are later milestones.

## Next work

1. Validate daily workflows with canteen managers and identify a pilot.
2. Confirm the stack, hosting, payment scope, costing approach, and actual
   branch details needed for the demonstration.
3. Bootstrap the web/API projects and document reproducible local commands.
4. Implement organization, school, user membership, and database isolation.
5. Seed and validate 250 products, then build receipt-to-sale-to-close flow.

## Verification and open decisions

This milestone is verified by inspecting the local folder and file
structure and reading back file contents. No application tests can run
before application code exists. Use `docs/Development roadmap.md` for
subsequent evidence-based acceptance gates.

Open: launch customer, confirmed operational workflows, provider accounts,
extraction accuracy on representative Arabic/English invoices, product
image sources, expected checkout load, retention policy, local deployment
obligations, and measured pilot success criteria. No real credentials or
customer data are required to use this scaffold.
