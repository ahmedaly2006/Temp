# Database design

Status: logical starter model. No migrations or policies have been applied.

## Conventions and isolation

Use UUID identifiers. Every tenant-owned record carries `organization_id`;
branch-owned records also carry `school_id`. Tenant ownership must remain
consistent across relationships: use composite foreign keys/unique targets
such as `(organization_id, id)` and include school scope where required.
Application checks supplement database constraints and policies.

Use integer minor units for final money amounts, a currency code, and exact
decimal types for rates and intermediate unit costs. Define one explicit
rounding policy before posting. Use exact quantities in a product's base
unit: discrete bottles require whole units, while weighed products may allow
defined precision. Store event timestamps as UTC-aware instants and keep
the branch's time zone and business date explicit.

## Principal tables

| Entity group | Key records and fields |
| --- | --- |
| Tenant and identity | `organizations`; `schools` with code/currency/time zone; `users` linked to auth identity; organization memberships, roles/permissions, school assignments |
| Catalog | `products` with SKU/barcode/base unit/expiry flag; categories; `product_images` with source/license/alt text; `branch_product_settings` with active flag/price/reorder point |
| Suppliers | `suppliers`, supplier-product references, pack conversions and price history |
| Purchasing | `purchase_orders` and lines; `invoices`, lines, original file/hash, extraction attempts and reviewed version; approval events |
| Receipts | `goods_receipts` and lines referencing invoice/order lines, actual received units, unit cost, receiver/approver, lot/expiry where applicable |
| Stock | `inventory_movements`, transactional balance projection, lots, transfers and transfer lines, stock counts and count lines |
| Sales | `sales`, `sale_items`, `payments`, `refunds` and refund items, original sale links, historical price/tax/cost snapshots |
| Cash | `cash_drawers`, `cash_sessions`, `cash_movements`, payouts/expenses, closing counts and manager review |
| Reliability | `audit_logs`, `idempotency_requests` with payload hash/result, durable job/outbox records |

Catalog data is shared within one organization, not globally across customer
organizations. A platform-role assignment is separate from tenant membership.

## Inventory ledger

Each movement records organization, school, product, optional lot, signed
base-unit quantity, movement type, source record and line, actor, timestamp,
reason, and optional reversal link. Types include opening, receipt, sale,
return, waste, expiry, count adjustment, and transfer in/out.

On-hand stock equals the sum of posted movements for its scope. A cached
balance is a transactionally maintained projection, never an independent
editable truth. For the first demo, reject sales that would create negative
on-hand stock. Use row locks/conditional updates so concurrent transactions
cannot each consume the same remaining units. Reconcile projections to the
ledger in tests and operational checks.

Track count snapshots and subsequent movements explicitly; use a brief
branch/product counting lock initially, or a tested cutoff-and-replay
approach, to avoid counting sales made during a physical count twice.
Record discrepancies and require approval before adjustment posting.
Transfers preserve both branch trails. If delivery is not immediate, use an
in-transit state and destination receipt confirmation rather than adding
destination stock before it arrives.

## Financial history

Keep immutable sale-time unit price, discounts, tax amounts, currency, and
unit cost snapshots so later catalog changes cannot rewrite old reports.
Proposed initial costing is moving weighted average; confirm this before
implementation and document treatment of returns, wastage, and rounding.
A refund must reference original sale lines, respect remaining refundable
quantities/value, and separately record whether goods are restockable.

Cash entries use signed cash deltas and source links. Net cash received from
a cash sale is tender minus change, equal to the cash-funded part of the
sale. Electronic tenders have no physical-cash movement. Store opening float
once in the session; do not duplicate it as both float and a paid-in entry.

## Constraints and indexes

- Unique organization SKU and branch code; unique branch product setting.
  Decide barcode uniqueness/pack mapping deliberately rather than assuming
  every package is interchangeable.
- Unique `(organization_id, operation, idempotency_key)` with payload hash
  validation. Repeated identical requests return the stored result.
- One ledger effect per business source, line, and effect type. A retry must
  not create another movement for the same receipt/sale/refund event.
- Scope invoice-number duplicate rules to organization and supplier, adding
  an appropriate supplier series/fiscal period when needed. File hashes are
  additional duplicate signals; legitimate copies require explicit review.
- One active session per drawer, and initially one per cashier. Scope both
  constraints to organization and define active/closing states consistently.
- Positive sale/receipt quantities, nonzero movement deltas, valid currency,
  valid state transitions, and refund totals bounded by the original sale.
  Enforce cross-row totals in the posting transaction, not simple CHECKs.
- Index tenant and branch filters with timestamp/product/source keys. Index
  foreign keys and inspect query plans using representative demo data.

## Policies, retention, and migrations

Deny access by default. Tenant memberships and school assignments constrain
SELECT/INSERT/UPDATE/DELETE. Cashiers see only the data needed for their POS
and shift; managers are branch-limited; owners stay within their tenant.
Runtime roles cannot update/delete posted ledgers directly. Define approved
posting functions or tightly scoped server permissions and audit exceptions.
Do not cascade-delete financial history when disabling users/products.

Keep original invoice files private; database rows hold storage references,
hashes, and processing metadata rather than public document URLs. Retention
and privacy rules must be established before real data is used. Back up both
database and storage, and test restoration together.

Version schema and policy changes together and document their application
order. Test migrations from a clean database and from a prior schema;
evaluate any rollback/data-loss implications before production rollout.
