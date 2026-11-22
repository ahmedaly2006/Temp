# Cash reconciliation

Status: planned workflow. EGP amounts below are fictional test examples.

## Session lifecycle

```text
open → closing (count submitted; checkout blocked) → closed (manager reviewed)
```

An authenticated cashier starts a session on an assigned branch drawer and
records an opening float. Allow only one active session per drawer and,
initially, per cashier. Persist who opened it and when. Each sale/refund or
cash movement must identify its session and source.

While open, record cash sales, approved cash refunds, paid-ins, and payouts.
Electronic payments are reported separately. A canceled cart has no cash
effect; a posted sale requires an authorized reversal/refund rather than
deletion. Payouts and expenses require permission, a reason, and evidence
where applicable. Do not count one expense twice as both an expense payout
and a separate cash reduction.

When closing begins, block new checkout against that session and atomically
capture the closing cutoff. Show any unresolved transactions, then request
the physically counted cash, preferably with an optional denomination count.
A manager reviews discrepancies and finalizes the session. Preserve every
submitted count and reviewer decision; closed records remain immutable.

## Equations

```text
Expected cash = Opening cash + Cash sales − Cash refunds + Paid-ins − Payouts
Cash discrepancy = Counted cash − Expected cash
```

Positive discrepancy means overage; negative discrepancy means shortage.
Cash sales means net physical cash received after change, not gross tender.
For a EGP 30 sale paid with EGP 50 and EGP 20 change, the drawer increases
by EGP 30. For split tender, include only the cash-funded amount.
Store the opening float once and exclude it from paid-ins to avoid doubling.

| Item | EGP |
| --- | ---: |
| Opening float | 500.00 |
| Cash sales | +2,000.00 |
| Approved cash refunds | −100.00 |
| Paid-ins | +200.00 |
| Payouts | −150.00 |
| **Expected cash** | **2,450.00** |
| Physically counted cash | 2,400.00 |
| **Discrepancy** | **−50.00** |
| Electronic sales, shown separately | 800.00 |

The EGP 800 electronic total does not change expected physical cash.
A discrepancy prompts investigation and notes; it is not evidence that a
particular employee stole money. Do not generate an artificial paid-in or
payout to hide a variance.

## Manager review and correction

Show branch, drawer, cashier, session start/cutoff/close, transaction count,
cash and other payment totals, refunds, paid-ins, payouts, expected cash,
counted cash, discrepancy, and source transaction links. Record the
reviewer's decision, timestamp, and explanation for a discrepancy.

Prevent close/checkout races with transaction locks or equivalent state
checks. Repeated close requests must return the same result. If a submitted
count needs correction before finalization, retain prior count versions and
require a reason. After closure, use a separately audited correction and
report amendment without rewriting the original session or hiding variance.
Refunds for earlier sales affect the current authorized session actually
paying the cash, with a link to the original transaction.

## Daily branch and owner close

Aggregate sessions by explicit branch business date and time zone. Surface
open/closing sessions so the manager cannot accidentally label an incomplete
day final. Show unresolved cash and stock discrepancies separately. An owner
can compare branches and review consolidated same-currency results; do not
treat expected drawer cash as sales revenue or profit.

Define cash-removal timing explicitly. A deposit/pickup before counting is
an authorized payout; a removal after the finalized count is a separate
custody/deposit event and does not retroactively alter the close. For the
next session, record only the actual retained float as opening cash.

## Acceptance checks

Verify the worked example, exact-money rounding, tender/change and split
payments, non-cash exclusion, refunds from earlier sessions, permission
denial, duplicate movements, duplicate closing, concurrent sales/close,
counted-cash version history, timezone boundaries, incomplete daily closes,
and reconciliation of report totals to source cash movements.
