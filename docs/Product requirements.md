# Product requirements

Status: starter specification, 2026-11-22. All application capabilities
below are planned unless `PROJECT_STATUS.md` records their implementation.

## Purpose and first release

Help canteen operators account for deliveries, stock, sales, staff cash
drawers, and daily results across multiple schools. The first end-to-end
release must support invoice review → actual goods receipt → stock ledger
→ cashier checkout → physical stock count and cash closing → owner report.
Validate the workflow with 5–10 operators and one prospective pilot school.

## Users and access

| Role | Scope and capabilities |
| --- | --- |
| Platform super admin | Customer organizations, account status, subscription metadata, technical health, and explicitly authorized/audited support access |
| Business owner/admin | All schools within their organization; staff, catalog, prices, suppliers, invoices, stock, expenses, reports, and approvals |
| Branch manager | Assigned schools only; permitted invoice review, stock counts, adjustments, refunds, and cash closing |
| Cashier/worker | Assigned branch POS, own transactions and cash session, opening/closing counts, and refund requests |

Provide separate admin and cashier login experiences with individual
accounts, secure password handling, rate limiting, session expiration, and
server-side permissions. UI visibility alone never grants or enforces
access. Platform administration must not silently grant tenant data access.

## Fictional demonstration

One sample operating organization has AIS-WEST (West Campus/Sheikh Zayed,
default) and AIS-MAIN (Main Campus) demo branches. These are planning labels,
not verified current campus coverage. Show “Demonstration — Not affiliated
with AIS Egypt.” Use simulated suppliers, prices, stock, and transactions;
avoid official logos, real student records, and any partnership claim.

Use one shared master catalog of exactly 250 unique products and different
opening balances at each branch. Seed at least four cashiers per branch,
one manager per branch, one owner, one platform administrator, multiple
suppliers, invoices, open/closed shifts, stock shortages, and cash/stock
discrepancy examples. Generate development-only credentials securely.

| Category | Product count |
| --- | ---: |
| Water and soft drinks | 35 |
| Juices | 30 |
| Milk and dairy drinks | 20 |
| Chips and salty snacks | 35 |
| Chocolate and sweets | 35 |
| Biscuits and crackers | 25 |
| Sandwiches and wraps | 25 |
| Pastries and baked goods | 20 |
| Fruits and healthy snacks | 15 |
| Ice cream and frozen treats | 10 |
| **Total** | **250** |

Each product needs an ID, unique SKU, English name, optional Arabic name,
category/subcategory, brand when relevant, size, base unit, optional barcode,
purchase cost, selling price, explicit tax configuration, supplier reference,
active flag, image/alt text, and expiry-tracking flag. Branch settings hold
availability, price overrides, reorder levels, and independent balances.
Opening stock must be posted as traceable movements. Store the catalog as
reusable seed data with CSV/JSON export, not only frontend arrays.

Every product must have a licensed image with source/attribution recorded
or a clearly identified product-specific illustration/fallback. Use
consistent containment and dimensions; never fabricate working image URLs.

## Core capabilities

- **POS:** product pictures, categories, search, favorites, barcode input,
  cart quantities/removal, cash tender/change, optional separate non-cash
  tenders, receipt display, and optional printing. Associate every sale with
  a cashier, branch, and open cash session. Atomically record the sale,
  payment, and inventory movements; retry safely without duplicate posting.
- **Invoices:** JPG/PNG/PDF/Excel/CSV upload, secure original storage,
  extraction or structured parsing, editable product matching, carton
  conversion, duplicate checks, partial receipts, approval, and audit history.
- **Inventory:** receipts, sales, approved refunds, stock transfers, wastage,
  expiry, reorder thresholds, movement history, and physical-count drafts,
  discrepancy review, approval, and export.
- **Cash:** drawer/session opening balance, cash sales/refunds, approved
  paid-ins/payouts and expenses, counted closing cash, variance, manager
  review, branch daily close, and consolidated owner view.
- **Owner reports:** daily/weekly/monthly sales, branch and cashier results,
  product popularity, purchase costs, gross profit estimates, cash variance,
  low stock, wastage, and stock count differences. Include CSV/Excel export;
  printable PDF is optional. Derive figures from actual demo transactions.
- **Purchasing:** supplier directory, product links, historical costs,
  purchase orders, outstanding deliveries, and price-change flags. Add
  reorder suggestions after reliable stock and sales history exist.

## Experience and operational requirements

Use a professional navy/charcoal/white palette with restrained teal accents,
consistent typography, clear navigation, and responsive desktop/tablet/mobile
layouts. Plan light/dark modes, accessible contrast, keyboard operation,
large touch targets, and clear loading/empty/error states. Prioritize
cashier speed and owner information clarity. English comes first; keep
translation and Arabic right-to-left layout possible.

Target 2–5 seconds of software interaction for a typical one-to-three-item
checkout, excluding payment handling; validate this with users. Online
checkout is the initial baseline. Show connection failures clearly and do
not display an unconfirmed sale as complete. Offline queuing requires a
separate implemented and tested synchronization design.

Keep organizations isolated in storage and API requests, collect minimal
personal data, record privileged actions, and plan monitored backups and
restore testing. Use EGP in the demo, configurable branch time zones, UTC
event storage, and branch-local reporting dates. Assess production privacy,
tax/receipt, and payment requirements for the actual launch location before
live operation; this specification does not establish compliance.

## Acceptance criteria for the first complete demonstration

1. All four roles enforce permissions across two branches and a separate
   test organization, including direct API and database access attempts.
2. Exactly 250 unique products satisfy the category totals and image rules;
   each branch has its own reconciled opening inventory.
3. Approving ten cartons of 24 bottles adds 240 units once. Repeating the
   request adds nothing; receiving 45 of 50 units adds only 45.
4. Checkout records one sale/payment/stock change despite retries; two
   cashiers racing for the final unit cannot both oversell it.
5. Expected 180 units versus counted 176 produces a reviewed −4 adjustment
   with an audit trail, without silently overwriting stock.
6. Cash closing follows the equations and worked example in the cash spec;
   electronic payments never inflate expected physical cash.
7. Dashboard and exported totals reconcile to posted transactions and obey
   tenant, branch, date, and role filters.

Later scope: advanced prediction, voice calling, WhatsApp, offline checkout,
subscriptions/payment billing integration, and country-specific expansion.
