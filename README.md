# LC Lifecycle — State Diagram with Voucher Entries

> Complete state machine for an Import Letter of Credit (LC) covering Acceptance → LTR Conversion → Final Settlement, with the journal voucher posted at each transition. Aligned with IAS 21, IAS 23, and Bangladesh banking practice (Usance LC + LTR facility).

---

## Table of Contents

- [Overview](#overview)
- [State Diagram](#state-diagram)
- [State Definitions](#state-definitions)
- [Transitions and Voucher Entries](#transitions-and-voucher-entries)
  - [State 1 → State 2: Acceptance](#state-1--state-2-acceptance)
  - [State 2 → State 3a: Direct Settlement](#state-2--state-3a-direct-settlement)
  - [State 2 → State 3b: LTR Conversion](#state-2--state-3b-ltr-conversion)
  - [State 3b → State 4: LTR Final Settlement](#state-3b--state-4-ltr-final-settlement)
- [State Transition Rules](#state-transition-rules)
- [ERP Implementation](#erp-implementation)
- [Key Insights](#key-insights)
- [Appendix — Worked Example](#appendix--worked-example-values)

---

## Overview

A Usance Letter of Credit follows a multi-stage lifecycle from opening through final settlement. Unlike a Sight LC (which settles immediately on document presentation), a Usance LC introduces a **deferred payment period** during which the importer carries an acceptance liability. If cash flow doesn't permit settlement at maturity, the liability can convert into an **LTR (Loan against Trust Receipt)** — a fresh short-term loan that extends the payment runway.

This document maps every state, every transition, and the journal voucher posted at each transition.

---

## State Diagram

```
                ┌─────────────────────────┐
                │    1. LC OPENED         │
                │  Margin deposited,      │
                │  charges paid           │
                └────────────┬────────────┘
                             │
                             │  Documents arrive at bank
                             │  Treasury accepts documents
                             ▼
                ┌─────────────────────────┐
                │  2. DOCUMENTS ACCEPTED  │
                │  Acceptance liability   │
                │  live until maturity    │
                └────────────┬────────────┘
                             │
                             │  LC tenor expires
                             │  (e.g., 90 days)
                             ▼
                    ┌────────────────┐
                    │   MATURITY     │
                    │   DECISION     │
                    └───┬────────┬───┘
                        │        │
              Pay now   │        │  Convert to LTR
                        │        │
                        ▼        ▼
        ┌────────────────────┐  ┌──────────────────────┐
        │ 3a. SETTLED        │  │ 3b. LTR CONVERTED    │
        │ Bank paid on       │  │ Trust receipt loan   │
        │ maturity (final)   │  │ 90–180 days runway   │
        └────────────────────┘  └──────────┬───────────┘
                                           │
                                           │  LTR matures
                                           │  Final payment
                                           ▼
                                ┌──────────────────────┐
                                │ 4. SETTLED (post LTR)│
                                │ Account closed       │
                                │ to zero              │
                                └──────────────────────┘
```

---

## State Definitions

| State | Code | Description | Active Liability Account |
|---|---|---|---|
| 1 | `OPENED` | LC application approved, margin deposited, opening charges paid. Documents not yet received. | LC Margin (asset side) |
| 2 | `ACCEPTED` | Shipping documents received and accepted. Goods can be cleared. Importer carries acceptance liability until maturity. | LC Acceptance Liability |
| 3a | `SETTLED` | Direct settlement on or before maturity. Final state. | None — closed |
| 3b | `LTR_CONVERTED` | Acceptance liability converted to LTR loan at maturity. Fresh maturity clock starts. | LTR Acceptance Liability |
| 4 | `SETTLED` (post LTR) | LTR loan repaid in full with interest. Final state. | None — closed |
| — | `CANCELLED` | LC cancelled before document acceptance (e.g., discrepancy, non-shipment). | None |

---

## Transitions and Voucher Entries

### State 1 → State 2: Acceptance

**Trigger:** Documents arrive at bank, importer accepts them after verification.

**Timing:** Typically Day 25–35 from LC opening, depending on shipment.

**Business meaning:** At this moment, the importer becomes **legally liable** to pay on the maturity date. The supplier has performed. Goods can be released for clearance and use.

**Voucher A — At Acceptance (JV)**

```
Dr.  LC-in-Transit (Goods Cost) — LC No. 0123       11,000,000.00
     Cr.  LC Margin A/C — LC No. 0123                          2,200,000.00
     Cr.  LC Acceptance Liability — 23020101001                8,800,000.00
```

**ERP status update:**

```python
lc.status = 'ACCEPTED'
lc.acceptance_date = date.today()
lc.maturity_date = date.today() + timedelta(days=lc.tenor_days)
lc.save()
```

> **Note:** LC-in-Transit continues to accumulate other costs (insurance, freight, customs duty, C&F charges) before final transfer to inventory at GRN. The acceptance voucher only handles the goods-cost portion.

---

### State 2 → State 3a: Direct Settlement

**Trigger:** Importer pays the full acceptance liability on or before maturity date.

**Timing:** On maturity date (Day ~120 from LC opening for a 90-day Usance).

**Business meaning:** Cash flow permits direct payment. No further bank financing needed.

**Voucher B1 — Direct Settlement (BPV)**

```
Dr.  LC Acceptance Liability — 23020101001           8,800,000.00
Dr.  Exchange Loss A/C                                   44,000.00
     Cr.  Bank — CD A/C                                          8,844,000.00
```

**Calculation of exchange variance:**

| Item | Value |
|---|---:|
| FC liability (USD) | USD 80,000 |
| Acceptance rate (booked) | BDT 110.00 / USD |
| Settlement rate (actual) | BDT 110.55 / USD |
| Exchange Loss | USD 80,000 × (110.55 − 110.00) = BDT 44,000 |

**ERP status update:**

```python
lc.status = 'SETTLED'
lc.settlement_date = date.today()
lc.settlement_rate = settlement_rate
lc.save()
```

> **IAS 21 Rule:** Exchange differences on monetary items must hit Profit & Loss in the period they arise. **Never capitalize** these to inventory.

---

### State 2 → State 3b: LTR Conversion

**Trigger:** Importer requests bank to convert the acceptance liability to an LTR loan at maturity.

**Timing:** On or just before maturity date. Bank requires written application 5–7 days in advance.

**Business meaning:** Cash flow doesn't permit direct payment. Bank extends a fresh loan (against trust receipt on the goods) to pay off the LC supplier. Importer now owes the bank under LTR terms (typically 90–180 days, higher interest).

**Voucher B2 — LTR Conversion (JV)**

```
Dr.  LC Acceptance Liability — 23020101001           8,800,000.00
     Cr.  LTR Acceptance Liability — 23020101003               8,800,000.00
```

**If interest is charged at conversion (some banks):**

```
Dr.  LC Acceptance Liability — 23020101001           8,800,000.00
Dr.  Bank Interest Expense                              55,000.00
     Cr.  LTR Acceptance Liability — 23020101003               8,855,000.00
```

**ERP status update:**

```python
lc.status = 'LTR_CONVERTED'
lc.ltr_conversion_date = date.today()
lc.ltr_maturity_date = date.today() + timedelta(days=ltr_tenor_days)
lc.ltr_account_code = '23020101003'
lc.save()
```

> **Watch out:** This is a one-way conversion. Once an LC is LTR'd, it cannot revert to plain acceptance. The original LC account closes; the LTR account becomes the active liability.

---

### State 3b → State 4: LTR Final Settlement

**Trigger:** LTR loan matures.

**Timing:** Day 300–500 from LC opening (depending on LTR tenor of 90, 120, or 180 days).

**Business meaning:** Final payoff. Both principal and accumulated interest are paid to the bank. The LC + LTR chain closes completely.

**Voucher C — LTR Final Settlement (BPV)**

```
Dr.  LTR Acceptance Liability — 23020101003          8,800,000.00
Dr.  Bank Interest Expense (LTR period)                180,000.00
     Cr.  Bank — CD A/C                                          8,980,000.00
```

**Calculation of LTR interest:**

| Item | Value |
|---|---:|
| LTR Principal | BDT 8,800,000 |
| LTR Interest Rate | 9.0% p.a. |
| LTR Period | 90 days |
| Interest | 8,800,000 × 9% × 90/365 = BDT 195,287 (≈ 180,000 simple) |

**ERP status update:**

```python
lc.status = 'SETTLED'
lc.final_settlement_date = date.today()
lc.save()
```

> **IAS 23 Rule:** LTR interest is **financing cost**, not inventory cost. It hits P&L as Bank Interest Expense, never capitalized — because raw material is not a "qualifying asset" under IAS 23.

---

## State Transition Rules

The state machine must enforce these allowed transitions and reject all others:

| From State | Allowed Next State(s) | Trigger | Approval Required |
|---|---|---|---|
| `OPENED` | `ACCEPTED`, `CANCELLED` | Document acceptance / Discrepancy | Treasury Manager |
| `ACCEPTED` | `SETTLED`, `LTR_CONVERTED`, `CANCELLED` | Payment / LTR sanction / Recall | CFO |
| `LTR_CONVERTED` | `SETTLED` | LTR maturity payment | CFO |
| `SETTLED` | — (terminal) | — | — |
| `CANCELLED` | — (terminal) | — | — |

### Forbidden Transitions

These must be blocked at the application layer:

- ❌ `OPENED → SETTLED` (must accept documents first)
- ❌ `OPENED → LTR_CONVERTED` (no liability exists yet)
- ❌ `SETTLED → ACCEPTED` (settlements are final and irreversible)
- ❌ `LTR_CONVERTED → ACCEPTED` (one-way conversion)
- ❌ `SETTLED → LTR_CONVERTED` (cannot un-settle)

---

## ERP Implementation

### Django Model with State Machine

```python
from django.db import models
from django.core.exceptions import ValidationError
from datetime import timedelta


class LcMaster(models.Model):
    STATUS_CHOICES = [
        ('OPENED', 'LC Opened'),
        ('ACCEPTED', 'Documents Accepted'),
        ('LTR_CONVERTED', 'Converted to LTR'),
        ('SETTLED', 'Settled'),
        ('CANCELLED', 'Cancelled'),
    ]

    ALLOWED_TRANSITIONS = {
        'OPENED': ['ACCEPTED', 'CANCELLED'],
        'ACCEPTED': ['SETTLED', 'LTR_CONVERTED', 'CANCELLED'],
        'LTR_CONVERTED': ['SETTLED'],
        'SETTLED': [],
        'CANCELLED': [],
    }

    lc_no = models.CharField(max_length=50, unique=True)
    fc_currency = models.CharField(max_length=3)
    fc_amount = models.DecimalField(max_digits=18, decimal_places=2)
    tenor_days = models.IntegerField(default=90)

    # Rate captures at each transition
    opening_rate = models.DecimalField(max_digits=10, decimal_places=4)
    acceptance_rate = models.DecimalField(
        max_digits=10, decimal_places=4, null=True
    )
    settlement_rate = models.DecimalField(
        max_digits=10, decimal_places=4, null=True
    )

    # Date captures at each transition
    opening_date = models.DateField()
    acceptance_date = models.DateField(null=True)
    maturity_date = models.DateField(null=True)
    ltr_conversion_date = models.DateField(null=True)
    ltr_maturity_date = models.DateField(null=True)
    settlement_date = models.DateField(null=True)

    # Account mappings
    margin_account = models.CharField(max_length=20)
    acceptance_account = models.CharField(max_length=20, blank=True)
    ltr_account = models.CharField(max_length=20, blank=True)

    status = models.CharField(
        max_length=20, choices=STATUS_CHOICES, default='OPENED'
    )

    def transition_to(self, new_status, user, **kwargs):
        """Atomic state transition with voucher posting hook."""
        if new_status not in self.ALLOWED_TRANSITIONS[self.status]:
            raise ValidationError(
                f"Cannot transition from {self.status} to {new_status}"
            )

        old_status = self.status
        self.status = new_status

        # Trigger voucher posting based on transition
        if old_status == 'OPENED' and new_status == 'ACCEPTED':
            self._post_acceptance_voucher(user, **kwargs)
            self.acceptance_date = kwargs.get('acceptance_date')
            self.maturity_date = (
                self.acceptance_date + timedelta(days=self.tenor_days)
            )
            self.acceptance_rate = kwargs.get('acceptance_rate')

        elif old_status == 'ACCEPTED' and new_status == 'SETTLED':
            self._post_direct_settlement_voucher(user, **kwargs)
            self.settlement_date = kwargs.get('settlement_date')
            self.settlement_rate = kwargs.get('settlement_rate')

        elif old_status == 'ACCEPTED' and new_status == 'LTR_CONVERTED':
            self._post_ltr_conversion_voucher(user, **kwargs)
            self.ltr_conversion_date = kwargs.get('conversion_date')
            self.ltr_maturity_date = (
                self.ltr_conversion_date
                + timedelta(days=kwargs.get('ltr_tenor_days', 90))
            )

        elif old_status == 'LTR_CONVERTED' and new_status == 'SETTLED':
            self._post_ltr_settlement_voucher(user, **kwargs)
            self.settlement_date = kwargs.get('settlement_date')
            self.settlement_rate = kwargs.get('settlement_rate')

        self.save()
        LcStatusLog.objects.create(
            lc=self,
            from_status=old_status,
            to_status=new_status,
            changed_by=user,
        )
```

### Audit Log Model

```python
class LcStatusLog(models.Model):
    lc = models.ForeignKey(
        LcMaster, on_delete=models.PROTECT, related_name='status_history'
    )
    from_status = models.CharField(max_length=20)
    to_status = models.CharField(max_length=20)
    changed_by = models.ForeignKey('auth.User', on_delete=models.PROTECT)
    changed_at = models.DateTimeField(auto_now_add=True)
    remarks = models.TextField(blank=True)

    class Meta:
        ordering = ['-changed_at']
```

### Voucher Posting Hook (Skeleton)

```python
def _post_acceptance_voucher(self, user, **kwargs):
    """Post Voucher A — at acceptance."""
    bdt_amount = self.fc_amount * self.acceptance_rate
    margin_amount = bdt_amount * Decimal('0.20')  # 20% margin example
    liability_amount = bdt_amount - margin_amount

    voucher = Voucher.objects.create(
        voucher_type='JV',
        voucher_date=self.acceptance_date,
        narration=f"Acceptance of documents under {self.lc_no}",
        created_by=user,
    )
    VoucherDetail.objects.bulk_create([
        VoucherDetail(
            voucher=voucher,
            account_code='1305001',  # LC-in-Transit Goods Cost
            debit=bdt_amount, credit=0,
            lc=self,
        ),
        VoucherDetail(
            voucher=voucher,
            account_code=self.margin_account,
            debit=0, credit=margin_amount,
            lc=self,
        ),
        VoucherDetail(
            voucher=voucher,
            account_code=self.acceptance_account,
            debit=0, credit=liability_amount,
            lc=self,
        ),
    ])
```

---

## Key Insights

### 1. The Liability Migrates — It Doesn't Disappear

The same obligation flows through multiple accounts:

```
LC Margin (asset)
     ↓ at acceptance
LC Acceptance Liability
     ↓ at LTR conversion
LTR Acceptance Liability
     ↓ at final settlement
Bank — CD A/C (cash outflow)
```

Reconciliation reports must trace this chain end-to-end. Any "leakage" (a balance that doesn't transfer cleanly) signals a posting error.

### 2. Each Transition Is a Fresh Voucher

Never amend or reverse the original acceptance voucher when converting to LTR. Always post a new JV that closes the old liability and opens the new one. Reversing historical vouchers is an audit red flag.

### 3. Exchange Rate Captures at Every Transition

Store separately on `lc_master`:

- `opening_rate` — the rate at LC application
- `acceptance_rate` — the rate when documents accepted
- `settlement_rate` — the rate when bank paid

Differences between these rates drive Exchange Gain/Loss postings per IAS 21.

### 4. Interest Is Always Financing Cost (Not Inventory)

LTR interest, PAD interest, LIM interest — all are **financing costs** under IAS 23. They hit Bank Interest Expense in P&L. Never capitalize to LC-in-Transit, even though the loan was raised to settle a trade transaction.

**Exception:** If the LC is for a **qualifying asset** under IAS 23 (e.g., a substantial machinery import under construction), interest *during the construction period* may be capitalized to CWIP. Raw materials, spare parts, and consumables never qualify.

### 5. Per-Bank Sub-Account Discipline

Each bank facility (LC, LTR, STL) needs its own sub-ledger account, often broken down by individual loan reference. This enables:

- Bank statement reconciliation (one-to-one match with bank's records)
- Maturity dashboards (which loans mature when)
- Interest accrual at month-end (per facility, per rate)
- Audit traceability

Example structure (from real-world Standard Chartered Bank sub-ledger):

```
230201010   Standard Chartered Bank (Group)
├── 23020101001   LC 249424020436 Acceptance Liability
├── 23020101002   LC 249424020446 Acceptance Liability
├── 23020101003   LTRLC 249424020029 LTR Acceptance Liability
├── 23020101004   LTRLC 249424020411 LTR Acceptance Liability
└── 23020101007   SCB STL A/C No: 006762415
```

---

## Appendix — Worked Example Values

### Scenario

| Parameter | Value |
|---|---:|
| LC Type | Usance, 90 days |
| FC Value | USD 100,000 |
| Margin | 20% |
| Tenor | 90 days |
| LTR Tenor (if used) | 90 days |
| LTR Interest Rate | 9.0% p.a. |
| Opening Rate | BDT 109.50 / USD |
| Acceptance Rate | BDT 110.00 / USD |
| Settlement Rate | BDT 110.55 / USD |

### Path A: Direct Settlement (No LTR)

| Day | Event | Voucher | Net Cash Outflow |
|---:|---|---|---:|
| 0 | LC Opened | Margin BDT 2,190,000 | 2,190,000 |
| 30 | Documents Accepted | (no cash) | — |
| 120 | Direct Settlement | BDT 8,844,000 | 8,844,000 |
| | **Total Cash Outflow** | | **11,034,000** |
| | **Plus exchange loss to P&L** | | 44,000 |

### Path B: With LTR Conversion

| Day | Event | Voucher | Net Cash Outflow |
|---:|---|---|---:|
| 0 | LC Opened | Margin BDT 2,190,000 | 2,190,000 |
| 30 | Documents Accepted | (no cash) | — |
| 120 | Convert to LTR | (no cash) | — |
| 210 | LTR Final Settlement | BDT 8,980,000 | 8,980,000 |
| | **Total Cash Outflow** | | **11,170,000** |
| | **Plus interest expense to P&L** | | 180,000 |

**Cost of LTR option:** BDT 136,000 extra (vs direct settlement). This is the "price" of the 90-day cash flow extension.

---

## License

This document is provided for educational and ERP-implementation reference. Adjust GL codes, tax rates, and exchange rates per your chart of accounts and the applicable NBR / Bangladesh Bank circular in effect at transaction date.
