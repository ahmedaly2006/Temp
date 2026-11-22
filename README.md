# Vexoryna AI

Vexoryna AI is a planned multi-tenant school-canteen management platform,
beginning with an Egypt-focused demonstration. Its core workflow is:

**Receive a supplier invoice → review actual delivery → post stock → sell
through cashier POS → reconcile stock and cash → review branch results.**

## Current state

This is a project scaffold with starter specifications, created on
2026-11-22. No runnable application, database, authentication, AI integration,
seeded products, automated tests, or deployment is implemented yet. Empty
implementation folders contain `.gitkeep` files so Git can retain them when
the project is committed. A Git repository has not been initialized by this
scaffold operation.

## Product direction

- Separate owner/admin dashboard and fast cashier/worker POS.
- Platform administrator, business owner, branch manager, and cashier roles.
- Many customer organizations, each with independently managed schools.
- A shared organization catalog with separate branch stock, prices, staff,
  cash sessions, and reporting.
- Invoice extraction with human review, carton-to-unit conversion, partial
  deliveries, duplicate detection, and traceable inventory movements.
- Physical stock counts, refunds, transfers, expiry/wastage, daily closing,
  and consolidated reporting.

The planned fictional AIS demonstration uses the labels AIS West Campus
(Sheikh Zayed, `AIS-WEST`, default) and AIS Main Campus (`AIS-MAIN`). These
labels come from the planning conversation; current campus details have not
been independently verified here. Display “Demonstration — Not affiliated
with AIS Egypt.” Use no official logos or real school/student records.

The demonstration will contain exactly 250 unique products with images or
clearly identified product-specific visual fallbacks, using EGP and separate
branch balances. Those records and images are future implementation work.

## Project structure

```text
Vexoryna ai/
├── apps/
│   ├── web/
│   └── api/
│       └── app/modules/
│           ├── organizations/
│           ├── schools/
│           ├── products/
│           ├── inventory/
│           ├── invoices/
│           ├── sales/
│           ├── cash/
│           └── reports/
├── database/
│   ├── migrations/
│   └── policies/
├── tests/
│   ├── e2e/
│   ├── integration/
│   ├── security/
│   └── load/
├── docs/
│   ├── Product requirements.md
│   ├── System architecture.md
│   ├── Database design.md
│   ├── Invoice processing.md
│   ├── Cash reconciliation.md
│   └── Development roadmap.md
├── .github/
│   └── ISSUE_TEMPLATE/
├── AGENTS.md
├── CLAUDE.md
├── PROJECT_STATUS.md
└── README.md
```

## Start here

1. Read [Product requirements](docs/Product%20requirements.md) for scope,
   roles, demo data, and acceptance criteria.
2. Read [System architecture](docs/System%20architecture.md) and
   [Database design](docs/Database%20design.md) before implementing storage.
3. Implement the workflows in [Invoice processing](docs/Invoice%20processing.md)
   and [Cash reconciliation](docs/Cash%20reconciliation.md).
4. Follow [Development roadmap](docs/Development%20roadmap.md) and keep
   [PROJECT_STATUS.md](PROJECT_STATUS.md) accurate.

Proposed stack: Next.js/React/TypeScript for the web app, FastAPI/Python for
the API, PostgreSQL with Supabase Auth/storage where suitable. These are
planning choices, not installed dependencies. Pin versions and document
actual setup and test commands when the application is bootstrapped.

No credentials are included. Generate development-only demo accounts during
later seeding and keep secrets out of source control. Before any live pilot,
validate workflows, backups, permissions, privacy, and applicable local POS
and invoicing obligations. This scaffold makes no compliance claim.
