# Letter of Credit (LC) — Accounting Voucher Cycle

> Event-wise journal entries with worked values for an importer under accrual accounting (IAS 2 / IAS 21). Aligned with Bangladesh import practice and typical ERP implementation patterns.

---

## Table of Contents

- [Scenario / Assumptions](#scenario--assumptions)
- [Event 1 — LC Margin Deposit](#event-1--lc-margin-deposit)
- [Event 2 — LC Opening Charges](#event-2--lc-opening-charges)
- [Event 3 — Marine Insurance Premium](#event-3--marine-insurance-premium)
- [Event 4 — Retirement of Documents](#event-4--retirement-of-documents--foreign-remittance)
- [Event 5 — Customs Duty, RD, VAT, AIT](#event-5--customs-duty-rd-vat-ait-at-port)
- [Event 6 — C&F Agent Bill](#event-6--cf-agent-bill)
- [Event 7 — Inland Freight](#event-7--inland-freight)
- [Event 8 — Goods Receipt Note (GRN)](#event-8--goods-receipt-note-grn--final-capitalization)
- [Consolidated Cost Build-up](#consolidated-cost-build-up)
- [Key Accounting Principles](#key-accounting-principles)
- [ERP Design Notes](#erp-design-notes)

---

## Scenario / Assumptions

| Parameter | Value |
|---|---|
| LC Type | Sight LC (At-sight payment) |
| Imported Item | Coal — Raw Material for cement production |
| LC Value (FC) | USD 100,000.00 |
| Exchange Rate at Opening | BDT 109.50 / USD |
| Exchange Rate at Retirement | BDT 110.00 / USD |
| LC Margin | 20% of FC value |
| Bank Financing (PAD) | 80% of FC value |
| Customs Duty (CD) | 5% |
| Regulatory Duty (RD) | 3% |
| Supplementary Duty (SD) | 0% (n/a for coal) |
| VAT at Import | 15% (adjustable) |
| AIT at Import | 5% (adjustable) |
| Insurance Premium | BDT 25,000 |
| C&F Agent Bill | BDT 45,000 + 15% VAT |
| Inland Freight | BDT 80,000 |

> **Note:** All amounts are illustrative. Capitalizable costs accumulate in **`LC-in-Transit (Goods in Transit)`** sub-ledgered by LC No., and transfer to Inventory at GRN. **VAT and AIT are not capitalized** — they are adjustable.

---

## Event 1 — LC Margin Deposit

On LC application, bank blocks 20% margin = USD 20,000 × 109.50 = **BDT 2,190,000**

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC Margin A/C — LC No. 0123/IMP/26 | 2,190,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 2,190,000.00 |
| **Total** | **2,190,000.00** | **2,190,000.00** |

**Voucher Type:** Bank Payment Voucher (BPV)
**Narration:** Being 20% margin deposited against LC No. 0123/IMP/26 for import of coal from XYZ Trading Co., USA.

---

## Event 2 — LC Opening Charges

Bank deducts opening charges (commission, SWIFT, stamp) plus 15% VAT.

| Component | Amount (BDT) |
|---|---:|
| LC Commission (0.40% on FC) | 43,800.00 |
| SWIFT Charges | 3,500.00 |
| Stamp / Postage | 1,200.00 |
| **Sub-total** | **48,500.00** |
| VAT @ 15% on charges | 7,275.00 |
| **Total deducted from CD A/C** | **55,775.00** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC No. 0123 (Bank Charges) | 48,500.00 | |
| VAT Current A/C (Adjustable) | 7,275.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 55,775.00 |
| **Total** | **55,775.00** | **55,775.00** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being LC opening charges debited by AB Bank against LC No. 0123/IMP/26.

---

## Event 3 — Marine Insurance Premium

Insurance taken from Sadharan Bima Corporation before shipment.

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC No. 0123 (Insurance) | 25,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 25,000.00 |
| **Total** | **25,000.00** | **25,000.00** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being marine insurance premium paid against LC No. 0123/IMP/26.

---

## Event 4 — Retirement of Documents / Foreign Remittance

Bank receives shipping documents and remits payment to foreign supplier.
FC value USD 100,000 × BDT 110.00 (retirement rate) = **BDT 11,000,000**.
Margin BDT 2,190,000 was deposited at rate 109.50; balance financed via PAD.

| Calculation | Amount |
|---|---:|
| FC Value (at retirement rate) | USD 100,000 × 110.00 = BDT 11,000,000 |
| Less: Margin already deposited | (BDT 2,190,000) |
| Bank Financing (PAD A/C) | BDT 8,810,000 |
| Exchange Loss on Margin | USD 20,000 × (110.00 − 109.50) = BDT 10,000 |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC No. 0123 (Goods Cost) | 11,000,000.00 | |
| Exchange Loss A/C | 10,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC Margin A/C — LC No. 0123 | | 2,190,000.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To PAD (Payment Against Document) A/C | | 8,810,000.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To Exchange Gain A/C (PAD portion, if any) | | 10,000.00 |
| **Total** | **11,010,000.00** | **11,010,000.00** |

**Voucher Type:** Journal Voucher
**Narration:** Being retirement of documents under LC No. 0123/IMP/26 — margin adjusted and balance financed via PAD.

> **Tip:** In ERP, post Dr/Cr exchange differences against a single `Exchange Gain/Loss` ledger. Many implementations net to a single line; here split for clarity.

---

## Event 5 — Customs Duty, RD, VAT, AIT at Port

Assessable Value (AV) = CIF (BDT 11,000,000 + Insurance 25,000 + Landing Charge 1% = 110,250) = **BDT 11,135,250**.
Duties calculated cumulatively per Bangladesh Customs practice.

| Component | Calculation | Amount (BDT) |
|---|---|---:|
| Assessable Value (AV) | — | 11,135,250.00 |
| Customs Duty (CD) | 5% on AV | 556,762.50 |
| Regulatory Duty (RD) | 3% on AV | 334,057.50 |
| VAT @ 15% | on (AV + CD + RD) | 1,803,910.50 |
| AIT @ 5% | on AV | 556,762.50 |
| **Total Payable to Customs** | | **3,251,493.00** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC No. 0123 (CD) | 556,762.50 | |
| LC-in-Transit — LC No. 0123 (RD) | 334,057.50 | |
| VAT Current A/C (Adjustable Import VAT) | 1,803,910.50 | |
| Advance Income Tax (AIT) — Adjustable | 556,762.50 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C (paid via Pay Order) | | 3,251,493.00 |
| **Total** | **3,251,493.00** | **3,251,493.00** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being customs duty, RD, VAT and AIT paid for clearance of consignment under LC No. 0123/IMP/26, B/E No. C-XXXXX.

> **Capitalization rule:** CD and RD are capitalized to inventory (irrecoverable). **VAT and AIT are not capitalized** — they sit in current asset accounts and adjust against output VAT / corporate tax respectively.

---

## Event 6 — C&F Agent Bill

| Component | Amount (BDT) |
|---|---:|
| C&F Service Charge | 45,000.00 |
| VAT @ 15% | 6,750.00 |
| **Gross Bill Value** | **51,750.00** |
| Less: VDS @ 7.5% on service value | (3,375.00) |
| Less: TDS @ 10% on service value | (4,500.00) |
| **Net Payable to C&F Agent** | **43,875.00** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC No. 0123 (C&F Charges) | 45,000.00 | |
| VAT Current A/C (Adjustable) | 6,750.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To C&F Agent — ABC Logistics Ltd. | | 43,875.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To VDS Payable @ 7.5% | | 3,375.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable @ 10% | | 4,500.00 |
| **Total** | **51,750.00** | **51,750.00** |

**Voucher Type:** Journal Voucher (Bill Booking)
**Narration:** Being C&F bill booked for ABC Logistics against LC No. 0123/IMP/26.

---

## Event 7 — Inland Freight

Movement of coal from Chittagong port to factory.

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC No. 0123 (Inland Freight) | 80,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To Transport Vendor — Star Carriers | | 76,000.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable @ 5% (Transport) | | 4,000.00 |
| **Total** | **80,000.00** | **80,000.00** |

**Voucher Type:** Journal Voucher
**Narration:** Being inland freight booked for movement of coal from Chittagong port to factory under LC No. 0123/IMP/26.

---

## Event 8 — Goods Receipt Note (GRN) — Final Capitalization

All accumulated capitalizable costs in `LC-in-Transit` are transferred to Raw Material Inventory. Auto-posted from the GRN module in ERP.

| Cost Component | Amount (BDT) |
|---|---:|
| LC Opening Charges | 48,500.00 |
| Insurance Premium | 25,000.00 |
| FC Value (Goods Cost) | 11,000,000.00 |
| Customs Duty (CD) | 556,762.50 |
| Regulatory Duty (RD) | 334,057.50 |
| C&F Charges | 45,000.00 |
| Inland Freight | 80,000.00 |
| **Total Inventory Cost** | **12,089,320.00** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| Raw Material Inventory — Coal | 12,089,320.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC-in-Transit — LC No. 0123 | | 12,089,320.00 |
| **Total** | **12,089,320.00** | **12,089,320.00** |

**Voucher Type:** Journal Voucher (auto from GRN)
**Narration:** Being raw material (coal) received at factory store under LC No. 0123/IMP/26, GRN No. GRN-2026-0456, all in-transit costs capitalized to inventory.

---

## Consolidated Cost Build-up

| # | Event | Capitalized to LC-in-Transit (BDT) | Other (Adjustable / Liability) |
|:-:|---|---:|---|
| 1 | LC Margin Deposit | — | Margin: 2,190,000 |
| 2 | LC Opening Charges | 48,500.00 | VAT: 7,275 |
| 3 | Marine Insurance | 25,000.00 | — |
| 4 | Document Retirement (FC Value) | 11,000,000.00 | PAD: 8,810,000 / Exch Loss: 10,000 |
| 5 | Customs Duty + RD | 890,820.00 | VAT: 1,803,910.50 / AIT: 556,762.50 |
| 6 | C&F Agent Bill | 45,000.00 | VAT: 6,750 / VDS+TDS: 7,875 |
| 7 | Inland Freight | 80,000.00 | TDS: 4,000 |
| 8 | GRN — Transfer to Inventory | (12,089,320.00) | → Raw Material Inventory |
| | **Total Inventory Cost** | **12,089,320.00** | |

---

## Key Accounting Principles

**1. Capitalization (IAS 2 — Inventories)**
All directly attributable costs of bringing inventory to its present location and condition are capitalized — including purchase price, import duties (CD, RD, SD), transport, handling, and bank charges directly tied to the import.

**2. Non-capitalizable items**
Recoverable taxes (VAT, AIT) are excluded from inventory cost and held as current assets adjustable against output liability.

**3. Foreign currency (IAS 21)**
Transactions are recorded at the spot rate on the date of transaction. Exchange differences between booking and settlement are posted to Exchange Gain/Loss (P&L).

**4. Sub-ledger discipline**
`LC-in-Transit` must be sub-ledgered by LC Number to enable clean GRN draw-down and audit traceability. Each LC's balance must reach zero after final GRN.

**5. Partial shipments**
If goods arrive in multiple consignments, `LC-in-Transit` balance is apportioned by quantity ratio at each GRN. The LC closes only when cumulative GRN qty equals LC qty.

**6. Capital LCs**
For machinery imports, the final debit on GRN goes to **Capital Work-in-Progress (CWIP)** instead of inventory. CWIP transfers to Fixed Asset on commissioning, and depreciation begins.

---

## ERP Design Notes

### Recommended Schema

```sql
-- LC Master
CREATE TABLE lc_master (
    lc_id            INT IDENTITY PRIMARY KEY,
    lc_no            VARCHAR(50) UNIQUE NOT NULL,
    lc_type          VARCHAR(20),  -- 'SIGHT' | 'USANCE' | 'CAPITAL'
    supplier_id      INT,
    bank_id          INT,
    fc_currency      CHAR(3),
    fc_amount        DECIMAL(18,2),
    opening_rate     DECIMAL(10,4),
    margin_pct       DECIMAL(5,2),
    opening_date     DATE,
    expiry_date      DATE,
    status           VARCHAR(20),  -- 'OPEN' | 'RETIRED' | 'CLOSED'
    created_by       INT,
    created_at       DATETIME
);

-- Event-wise charges
CREATE TABLE lc_charges (
    charge_id        INT IDENTITY PRIMARY KEY,
    lc_id            INT FOREIGN KEY REFERENCES lc_master(lc_id),
    event_type       VARCHAR(50),  -- 'MARGIN' | 'OPENING' | 'INSURANCE' |
                                   -- 'RETIREMENT' | 'CUSTOMS' | 'CNF' |
                                   -- 'FREIGHT' | 'OTHER'
    charge_date      DATE,
    amount           DECIMAL(18,2),
    is_capitalizable BIT,
    voucher_id       INT,
    remarks          NVARCHAR(500)
);

-- GRN linkage (supports partial shipments)
CREATE TABLE lc_grn_link (
    link_id          INT IDENTITY PRIMARY KEY,
    lc_id            INT FOREIGN KEY REFERENCES lc_master(lc_id),
    grn_id           INT,
    qty_received     DECIMAL(18,4),
    qty_ratio        DECIMAL(10,6),  -- for cost apportionment
    capitalized_amt  DECIMAL(18,2),
    grn_date         DATE
);
```

### Apportionment Logic for Partial Shipments

```python
def apportion_lc_cost(lc_id, grn_qty):
    """Apportion accumulated LC-in-Transit cost to a GRN by quantity ratio."""
    lc = LcMaster.objects.get(pk=lc_id)
    total_qty = lc.fc_quantity
    cumulative_received = LcGrnLink.objects.filter(lc_id=lc_id).aggregate(
        total=Sum('qty_received')
    )['total'] or Decimal('0')

    qty_ratio = grn_qty / total_qty
    in_transit_balance = LcCharges.objects.filter(
        lc_id=lc_id, is_capitalizable=True
    ).aggregate(total=Sum('amount'))['total'] or Decimal('0')

    # On final GRN, close out remaining balance to avoid rounding leakage
    is_final = (cumulative_received + grn_qty) >= total_qty
    if is_final:
        already_capitalized = LcGrnLink.objects.filter(lc_id=lc_id).aggregate(
            total=Sum('capitalized_amt')
        )['total'] or Decimal('0')
        return in_transit_balance - already_capitalized

    return (in_transit_balance * qty_ratio).quantize(Decimal('0.01'))
```

### Voucher Posting Hooks

| Trigger Point | Posting Source | Voucher Type |
|---|---|---|
| LC Application Saved | Margin Deposit module | BPV |
| Bank Charge Bill | Bank Reco / Manual JV | BPV |
| Insurance Bill Booked | AP Module | BPV / JV |
| Bank Retirement Notice | Treasury Module | JV |
| Bill of Entry Posted | Import Module | BPV |
| C&F Bill Booked | AP Module | JV |
| Inland Freight Bill | AP Module | JV |
| GRN Confirmed | Inventory Module | JV (auto) |

---

## Caveats & Localization

- **Duty rates are illustrative.** Look up actual HS code rates against the current NBR SRO. Coal HS codes have shifted in recent budgets.
- **VAT rates** vary — some raw materials have reduced rates or exemptions.
- **VDS / TDS rates** depend on the agent's status (registered vs. unregistered, company vs. individual) per the ITA and VAT Act.
- **Exchange rate** policy: most companies use Bangladesh Bank's published rate at retirement date. Some use the bank's actual conversion rate from the FX deal slip.

---

## License

This document is provided for educational and reference use. Adjust GL codes, tax rates, and exchange rates per your chart of accounts and the applicable NBR / Bangladesh Bank circular in effect at transaction date.
