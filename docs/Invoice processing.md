# Invoice processing

Status: planned workflow; no scanner or external AI connection exists yet.

## Goal and inputs

Turn a supplier document into a reviewed delivery without losing the
original evidence or accidentally adding duplicate stock. Accept JPG, PNG,
PDF, Excel, and CSV. Use structured parsing for spreadsheets/CSV and an
extraction adapter for scanned images/PDFs. Test representative Arabic and
English documents before claiming language accuracy.

## State and review workflow

```text
uploaded → extracting → needs_review → approved → partially_received → received
              |              |
              v              v
            failed        rejected
```

A fully delivered approval can move directly from approved to received.
Invoice approval and each receipt are separate events: approval validates
the purchasing document; a goods receipt records what physically arrived.
The UI may combine them in one action when all goods have arrived. A
receipt posts stock once, whether created during initial approval or later.

1. An authorized owner/manager selects a permitted school and supplier.
2. Validate file signature/type, size, parsing limits, and access; store the
   original privately and record its hash and uploader.
3. Create a retryable extraction job. Preserve processing mode, raw proposal,
   structured fields, warnings, source location, and proposal version.
4. Match lines by barcode/SKU/supplier mapping first, then suggest candidates
   from descriptions. Uncertain matches remain unresolved until reviewed.
5. Let the reviewer correct every field, pack conversion, product, tax, cost,
   and received quantity. Show invoice totals and any mismatches.
6. Check duplicate file/invoice signals and outstanding receipt quantities.
   Flag likely duplicates for explicit resolution; do not silently repost.
7. Approve the current reviewed version. Check authority and all required
   fields again on the server, including actual delivered units.
8. In one transaction, create the goods receipt, receipt lines, inventory
   movements, relevant cost history, approval/audit entries, and persisted
   idempotent result. Commit all or none.
9. Display the posted units and remaining outstanding quantity. Keep the
   original, edits, approvals, and receipt history available to authorized
   users. Later deliveries receive their own unique receipt IDs.

## Extracted and reviewed fields

Supplier name/reference, invoice number/date/currency, school, description,
matched product, invoiced quantity, packaging unit, units per pack, base
units invoiced, actual units received, unit purchase price and its basis
(carton or individual unit), discount, line subtotal/tax/total, invoice
subtotal/tax/total, and lot/expiry when relevant. Distinguish missing data
from zero; never silently guess a pack size or price basis.

## Unit and receipt examples

| Document/receipt | Result |
| --- | --- |
| 10 cartons × 24 bottles, all delivered | Add 240 bottles |
| 50 bottles invoiced, 45 delivered | Add 45; leave 5 outstanding |
| Remaining 5 bottles delivered later | Add 5 under a new receipt |
| Retry approval/receipt request | Return original result; add 0 extra |
| Carton size is missing or ambiguous | Block posting until reviewed |

If ten cartons cost EGP 240 each before tax/discount, the goods cost is EGP
2,400 and the base-unit cost is EGP 10 per bottle. Record pack and price
basis explicitly. Do not copy the carton price into the bottle cost.
Prevent cumulative receipts exceeding approved quantities unless an
authorized over-delivery decision is recorded.

## Accuracy and corrections

AI produces proposals only. Treat invoice text and extracted output as
untrusted data, validate against a strict schema, show uncertainty, and
require human review before any stock write. Use decimal arithmetic for
quantities/costs and an explicit rounding policy. Reconcile line totals with
invoice totals; explain unresolved differences and block approval where
required information is incomplete.

A retryable parser failure must leave inventory unchanged. If no provider
is configured, permit manual entry or explicitly labeled fixture extraction;
never imply a real document was processed by AI when it was not.

Detect concurrent edits through a reviewed version number. Preserve posted
records; correct mistakes through linked reversal/adjustment and reapproval,
not editing past ledger entries. Invoice approval does not itself mean the
supplier has been paid; payment status is a separate concern.

## Acceptance checks

Cover carton conversion, unit-price basis, partial and repeated deliveries,
duplicate files/numbers, retries and double-clicks, concurrent approval,
changed reviewed versions, unknown products, malformed inputs, total
mismatches, wrong-tenant access, parser failure, rollback after a failed
ledger write, manual correction, and explicit fixture-mode labeling.
