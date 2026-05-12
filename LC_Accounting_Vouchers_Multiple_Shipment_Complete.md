# LC Accounting — Multiple Shipment Scenario (Complete Cycle)

> Complete event-wise voucher cycle for an LC with partial shipments. Each shipment generates its own retirement voucher, PAD account, exchange rate capture, customs documents, and GRN entry. Aligned with IAS 2 / IAS 21 and Bangladesh import practice.

---

## Table of Contents

- [Overview](#overview)
- [Core Concept](#core-concept)
- [Scenario / Assumptions](#scenario--assumptions)
- [Event 1 — LC Margin Deposit](#event-1--lc-margin-deposit)
- [Event 2 — LC Opening Charges](#event-2--lc-opening-charges)
- [Event 3 — Marine Insurance Premium (Per Shipment)](#event-3--marine-insurance-premium-per-shipment)
- [Event 4 — Retirement of Documents (Per Shipment)](#event-4--retirement-of-documents-per-shipment)
  - [Event 4.1 — Shipment 1](#event-41--retirement-for-shipment-1)
  - [Event 4.2 — Shipment 2](#event-42--retirement-for-shipment-2)
  - [Event 4.3 — Shipment 3 (Final)](#event-43--retirement-for-shipment-3-final)
- [Reconciliation After All Retirements](#reconciliation-after-all-retirements)
- [Event 5 — Customs Duty, RD, VAT, AIT (Per Shipment)](#event-5--customs-duty-rd-vat-ait-per-shipment)
- [Event 6 — C&F Agent Bill (Per Shipment)](#event-6--cf-agent-bill-per-shipment)
- [Event 7 — Inland Freight (Per Shipment)](#event-7--inland-freight-per-shipment)
- [Event 8 — GRN Posting Per Shipment](#event-8--grn-posting-per-shipment)
- [Apportionment Methods](#apportionment-methods)
- [Critical ERP Design Considerations](#critical-erp-design-considerations)
- [Comparison — Single vs Multiple Shipment](#comparison--single-vs-multiple-shipment)

---

## Overview

Under a single LC with multiple shipments, **each shipment triggers its own document set**. The bank receives separate Bills of Lading, Invoices, and shipping documents for each consignment. Therefore each shipment is, accounting-wise, a **mini-LC under one umbrella LC**.

This is very common in cement industry imports — coal, clinker, gypsum, and limestone often arrive in 2–5 consignments under one LC, spread over weeks or months.

---

## Core Concept

| Aspect | Treatment |
|---|---|
| LC Margin Deposit (Event 1) | One-time at LC opening (LC-level, not per shipment) |
| LC Opening Charges (Event 2) | One-time at LC opening (LC-level, apportioned at GRN) |
| Marine Insurance (Event 3) | Per shipment (each consignment has its own policy) |
| Retirement (Event 4) | One per shipment (separate voucher per shipment) |
| Customs Duty, RD, VAT, AIT (Event 5) | Per Bill of Entry (one per shipment) |
| C&F Bill (Event 6) | Per shipment clearance |
| Inland Freight (Event 7) | Per shipment movement |
| GRN (Event 8) | Per shipment receipt |

---

## Scenario / Assumptions

| Parameter | Value |
|---|---|
| LC No. | 0123/IMP/26 |
| Imported Item | Coal — Raw Material for cement production |
| Total FC Value | USD 100,000 |
| Opening Rate | BDT 109.50 / USD |
| LC Margin (20%) | BDT 2,190,000 |
| Number of Shipments | 3 |
| **Shipment 1** | USD 40,000 (40%) — 400 MT — arrives early |
| **Shipment 2** | USD 35,000 (35%) — 350 MT — arrives mid |
| **Shipment 3** | USD 25,000 (25%) — 250 MT — arrives late |

### Exchange Rate Movement

| Event | Date | Rate (BDT/USD) |
|---|---|---:|
| LC Opening | Day 0 | 109.50 |
| Shipment 1 Retirement | Day 45 | 110.00 |
| Shipment 2 Retirement | Day 75 | 110.50 |
| Shipment 3 Retirement | Day 110 | 111.00 |

> **Note:** Per IAS 21, exchange differences on monetary items hit P&L — never capitalized to inventory.

---

## Event 1 — LC Margin Deposit

**Date:** Day 0  |  **Scope:** LC-level (one-time)

On LC application, bank blocks 20% margin against the **full LC value** (not per shipment). The margin is held as a single pool and apportioned later when each shipment is retired.

**Calculation:** USD 100,000 × 20% × 109.50 = **BDT 2,190,000**

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC Margin A/C — LC No. 0123/IMP/26 | 2,190,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 2,190,000.00 |
| **Total** | **2,190,000.00** | **2,190,000.00** |

**Voucher Type:** Bank Payment Voucher (BPV)
**Narration:** Being 20% margin deposited against LC No. 0123/IMP/26 for import of coal from XYZ Trading Co., USA (covers full LC value across all expected shipments).

> **Key Point:** Margin is deposited **once at the LC level**, not per shipment. It will be drawn down proportionally as each shipment retires (see Event 4).

---

## Event 2 — LC Opening Charges

**Date:** Day 0  |  **Scope:** LC-level (one-time)

Bank deducts opening charges (commission, SWIFT, stamp) plus 15% VAT. These are **LC-level costs** — they apply to the LC as a whole, not to individual shipments. They will be **apportioned across shipments at GRN time**.

| Component | Amount (BDT) |
|---|---:|
| LC Commission (0.40% on FC USD 100,000 × 109.50) | 43,800.00 |
| SWIFT Charges | 3,500.00 |
| Stamp / Postage | 1,200.00 |
| **Sub-total** | **48,500.00** |
| VAT @ 15% on charges | 7,275.00 |
| **Total deducted from CD A/C** | **55,775.00** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 (Bank Charges, LC-level) | 48,500.00 | |
| VAT Current A/C (Adjustable) | 7,275.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 55,775.00 |
| **Total** | **55,775.00** | **55,775.00** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being LC opening charges debited by AB Bank against LC No. 0123/IMP/26 (LC-level cost — to be apportioned across shipments at GRN).

> **Apportionment Note:** This BDT 48,500 will be split across the 3 shipments at GRN time:
> - Shipment 1 (40%): BDT 19,400
> - Shipment 2 (35%): BDT 16,975
> - Shipment 3 (25%): BDT 12,125
>
> See [Apportionment Methods](#apportionment-methods) section for the logic.

---

## Event 3 — Marine Insurance Premium (Per Shipment)

**Scope:** Per shipment (each shipment has its own policy)

Insurance is typically taken **per shipment** from Sadharan Bima Corporation (or a private insurer) because:
- Each shipment travels separately
- Premium is based on actual CIF value of that shipment
- Coverage period matches that shipment's transit time

### Event 3.1 — Insurance for Shipment 1

**Date:** Just before Shipment 1 sails  |  **Premium:** BDT 10,000

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 1 (Insurance) | 10,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 10,000.00 |
| **Total** | **10,000.00** | **10,000.00** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being marine insurance premium paid for Shipment 1 under LC No. 0123/IMP/26, Policy No. SBC-XXXXX.

### Event 3.2 — Insurance for Shipment 2

**Premium:** BDT 8,750

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 2 (Insurance) | 8,750.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 8,750.00 |
| **Total** | **8,750.00** | **8,750.00** |

**Voucher Type:** Bank Payment Voucher

### Event 3.3 — Insurance for Shipment 3

**Premium:** BDT 6,250

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 3 (Insurance) | 6,250.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 6,250.00 |
| **Total** | **6,250.00** | **6,250.00** |

**Voucher Type:** Bank Payment Voucher

> **Total Insurance across LC:** BDT 25,000 (same as single-shipment scenario, but split across 3 policies). Each amount goes directly to its respective shipment's sub-ledger — no apportionment needed.

---

## Event 4 — Retirement of Documents (Per Shipment)

### Event 4.1 — Retirement for Shipment 1

**Date:** Day 45  |  **Retirement Rate:** BDT 110.00 / USD

#### Calculation

| Item | Calculation | Amount (BDT) |
|---|---|---:|
| FC Value of Shipment 1 | USD 40,000 × 110.00 | 4,400,000.00 |
| Margin apportioned (40%) | 2,190,000 × 0.40 | 876,000.00 |
| PAD (Bank financing) | 4,400,000 − 876,000 | 3,524,000.00 |
| Exchange Loss on margin | USD 8,000 × (110.00 − 109.50) | 4,000.00 |

#### Voucher

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 1 (Goods Cost) | 4,400,000.00 | |
| Exchange Loss A/C | 4,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC Margin A/C — LC No. 0123 | | 876,000.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To PAD A/C — LC 0123 / Shipment 1 | | 3,524,000.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To Exchange Gain A/C (PAD portion) | | 4,000.00 |
| **Total** | **4,404,000.00** | **4,404,000.00** |

**Voucher Type:** Journal Voucher
**Narration:** Being retirement of documents for Shipment 1 (40% of LC value) under LC No. 0123/IMP/26 — partial margin adjusted, balance financed via PAD.

---

### Event 4.2 — Retirement for Shipment 2

**Date:** Day 75  |  **Retirement Rate:** BDT 110.50 / USD (rate moved)

#### Calculation

| Item | Calculation | Amount (BDT) |
|---|---|---:|
| FC Value of Shipment 2 | USD 35,000 × 110.50 | 3,867,500.00 |
| Margin apportioned (35%) | 2,190,000 × 0.35 | 766,500.00 |
| PAD (Bank financing) | 3,867,500 − 766,500 | 3,101,000.00 |
| Exchange Loss on margin | USD 7,000 × (110.50 − 109.50) | 7,000.00 |

#### Voucher

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 2 (Goods Cost) | 3,867,500.00 | |
| Exchange Loss A/C | 7,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC Margin A/C — LC No. 0123 | | 766,500.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To PAD A/C — LC 0123 / Shipment 2 | | 3,101,000.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To Exchange Gain A/C (PAD portion) | | 7,000.00 |
| **Total** | **3,874,500.00** | **3,874,500.00** |

**Voucher Type:** Journal Voucher
**Narration:** Being retirement of documents for Shipment 2 (35% of LC value) under LC No. 0123/IMP/26.

---

### Event 4.3 — Retirement for Shipment 3 (Final)

**Date:** Day 110  |  **Retirement Rate:** BDT 111.00 / USD (rate moved further)

#### Calculation

| Item | Calculation | Amount (BDT) |
|---|---|---:|
| FC Value of Shipment 3 | USD 25,000 × 111.00 | 2,775,000.00 |
| Margin apportioned (remaining 25%) | 2,190,000 × 0.25 | 547,500.00 |
| PAD (Bank financing) | 2,775,000 − 547,500 | 2,227,500.00 |
| Exchange Loss on margin | USD 5,000 × (111.00 − 109.50) | 7,500.00 |

#### Voucher

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 3 (Goods Cost) | 2,775,000.00 | |
| Exchange Loss A/C | 7,500.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC Margin A/C — LC No. 0123 | | 547,500.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To PAD A/C — LC 0123 / Shipment 3 | | 2,227,500.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To Exchange Gain A/C (PAD portion) | | 7,500.00 |
| **Total** | **2,782,500.00** | **2,782,500.00** |

**Voucher Type:** Journal Voucher
**Narration:** Being retirement of documents for Shipment 3 (final 25% of LC value) under LC No. 0123/IMP/26 — LC margin fully exhausted.

---

## Reconciliation After All Retirements

| Item | Shipment 1 | Shipment 2 | Shipment 3 | Total | Expected |
|---|---:|---:|---:|---:|---:|
| FC Value (USD) | 40,000 | 35,000 | 25,000 | **100,000** | 100,000 ✅ |
| Margin Used (BDT) | 876,000 | 766,500 | 547,500 | **2,190,000** | 2,190,000 ✅ |
| PAD Created (BDT) | 3,524,000 | 3,101,000 | 2,227,500 | **8,852,500** | — |
| BDT Goods Cost | 4,400,000 | 3,867,500 | 2,775,000 | **11,042,500** | — |
| Exchange Loss (BDT) | 4,000 | 7,000 | 7,500 | **18,500** | — |

### Key Checks

- ✅ LC Margin A/C balance after all retirements = **Zero** (fully consumed)
- ✅ LC Margin total used = BDT 2,190,000 = original deposit
- ✅ Sum of FC values across shipments = Total LC FC value

> **Note:** Total BDT goods cost (11,042,500) is **higher** than a single-retirement scenario (11,000,000) due to unfavorable rate movement across shipments. The BDT 42,500 difference is captured as Exchange Loss in P&L, **not** capitalized to inventory.

---

## Event 5 — Customs Duty, RD, VAT, AIT (Per Shipment)

Each shipment has its **own Bill of Entry (B/E)** at the port. CD, RD, VAT, and AIT are calculated separately on each shipment's Assessable Value.

### Event 5.1 — Customs for Shipment 1

**Assessable Value (AV) for Shipment 1:**
- CIF Value: BDT 4,400,000 + Insurance 10,000 + Landing Charge 1% (44,100) = **BDT 4,454,100**

| Component | Calculation | Amount (BDT) |
|---|---|---:|
| Customs Duty (CD) | 5% on AV | 222,705.00 |
| Regulatory Duty (RD) | 3% on AV | 133,623.00 |
| VAT @ 15% | on (AV + CD + RD) | 721,564.20 |
| AIT @ 5% | on AV | 222,705.00 |
| **Total Payable** | | **1,300,597.20** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 1 (CD) | 222,705.00 | |
| LC-in-Transit — LC 0123 / Shipment 1 (RD) | 133,623.00 | |
| VAT Current A/C (Adjustable) | 721,564.20 | |
| AIT (Adjustable) | 222,705.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 1,300,597.20 |
| **Total** | **1,300,597.20** | **1,300,597.20** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being customs duty, RD, VAT and AIT paid for clearance of Shipment 1 under LC No. 0123/IMP/26, B/E No. C-AAAAA.

### Event 5.2 — Customs for Shipment 2

**Assessable Value:** BDT 3,867,500 + Insurance 8,750 + Landing 1% (38,762.50) = **BDT 3,915,012.50**

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 2 (CD) | 195,750.63 | |
| LC-in-Transit — LC 0123 / Shipment 2 (RD) | 117,450.38 | |
| VAT Current A/C (Adjustable) | 634,232.03 | |
| AIT (Adjustable) | 195,750.63 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 1,143,183.67 |
| **Total** | **1,143,183.67** | **1,143,183.67** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being customs duty, RD, VAT and AIT paid for clearance of Shipment 2 under LC No. 0123/IMP/26, B/E No. C-BBBBB.

### Event 5.3 — Customs for Shipment 3

**Assessable Value:** BDT 2,775,000 + Insurance 6,250 + Landing 1% (27,812.50) = **BDT 2,809,062.50**

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 3 (CD) | 140,453.13 | |
| LC-in-Transit — LC 0123 / Shipment 3 (RD) | 84,271.88 | |
| VAT Current A/C (Adjustable) | 454,995.13 | |
| AIT (Adjustable) | 140,453.13 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To AB Bank — CD A/C | | 820,173.27 |
| **Total** | **820,173.27** | **820,173.27** |

**Voucher Type:** Bank Payment Voucher
**Narration:** Being customs duty, RD, VAT and AIT paid for clearance of Shipment 3 under LC No. 0123/IMP/26, B/E No. C-CCCCC.

> **Capitalization Rule:** CD and RD are capitalized to inventory. VAT and AIT are **adjustable** — they sit in current asset accounts.

---

## Event 6 — C&F Agent Bill (Per Shipment)

C&F agents bill separately for each shipment because each requires its own port handling, clearance, and documentation work.

### Event 6.1 — C&F for Shipment 1

| Component | Amount (BDT) |
|---|---:|
| C&F Service Charge | 18,000.00 |
| VAT @ 15% | 2,700.00 |
| **Gross Bill** | **20,700.00** |
| Less: VDS @ 7.5% | (1,350.00) |
| Less: TDS @ 10% | (1,800.00) |
| **Net Payable** | **17,550.00** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 1 (C&F) | 18,000.00 | |
| VAT Current A/C (Adjustable) | 2,700.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To C&F Agent — ABC Logistics | | 17,550.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To VDS Payable | | 1,350.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable | | 1,800.00 |
| **Total** | **20,700.00** | **20,700.00** |

**Voucher Type:** Journal Voucher (Bill Booking)

### Event 6.2 — C&F for Shipment 2

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 2 (C&F) | 15,750.00 | |
| VAT Current A/C (Adjustable) | 2,362.50 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To C&F Agent — ABC Logistics | | 15,356.25 |
| &nbsp;&nbsp;&nbsp;&nbsp;To VDS Payable | | 1,181.25 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable | | 1,575.00 |
| **Total** | **18,112.50** | **18,112.50** |

### Event 6.3 — C&F for Shipment 3

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 3 (C&F) | 11,250.00 | |
| VAT Current A/C (Adjustable) | 1,687.50 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To C&F Agent — ABC Logistics | | 10,968.75 |
| &nbsp;&nbsp;&nbsp;&nbsp;To VDS Payable | | 843.75 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable | | 1,125.00 |
| **Total** | **12,937.50** | **12,937.50** |

> **Total C&F across LC:** BDT 45,000 (18,000 + 15,750 + 11,250) — same as single-shipment, but properly attributed.

---

## Event 7 — Inland Freight (Per Shipment)

Each shipment moves separately from Chittagong port to the factory.

### Event 7.1 — Inland Freight for Shipment 1

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 1 (Freight) | 32,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To Transport Vendor — Star Carriers | | 30,400.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable @ 5% (Transport) | | 1,600.00 |
| **Total** | **32,000.00** | **32,000.00** |

**Voucher Type:** Journal Voucher

### Event 7.2 — Inland Freight for Shipment 2

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 2 (Freight) | 28,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To Transport Vendor — Star Carriers | | 26,600.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable @ 5% (Transport) | | 1,400.00 |
| **Total** | **28,000.00** | **28,000.00** |

### Event 7.3 — Inland Freight for Shipment 3

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| LC-in-Transit — LC 0123 / Shipment 3 (Freight) | 20,000.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To Transport Vendor — Star Carriers | | 19,000.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To TDS Payable @ 5% (Transport) | | 1,000.00 |
| **Total** | **20,000.00** | **20,000.00** |

> **Total Inland Freight across LC:** BDT 80,000 (32,000 + 28,000 + 20,000).

---

## Event 8 — GRN Posting Per Shipment

Each shipment closes to inventory **separately** as it physically arrives at the factory. The LC Opening Charges (LC-level) are apportioned across shipments by FC value ratio at this stage.

### GRN for Shipment 1

| Cost Component | Amount (BDT) |
|---|---:|
| Goods Cost (FC Value) | 4,400,000.00 |
| Apportioned LC Opening Charges (40%) | 19,400.00 |
| Insurance | 10,000.00 |
| Customs Duty (CD) | 222,705.00 |
| Regulatory Duty (RD) | 133,623.00 |
| C&F Charges | 18,000.00 |
| Inland Freight | 32,000.00 |
| **Total Shipment 1 Cost** | **4,835,728.00** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| Raw Material Inventory — Coal (Shipment 1) | 4,835,728.00 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC-in-Transit — LC 0123 / Shipment 1 | | 4,816,328.00 |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC-in-Transit — LC 0123 (LC-level apportioned) | | 19,400.00 |
| **Total** | **4,835,728.00** | **4,835,728.00** |

**Voucher Type:** Journal Voucher (auto from GRN)
**Narration:** Being raw material (coal) of Shipment 1 received at factory store under LC No. 0123/IMP/26, GRN No. GRN-2026-0456. LC-level charges apportioned by FC value ratio.

### GRN for Shipment 2

| Cost Component | Amount (BDT) |
|---|---:|
| Goods Cost | 3,867,500.00 |
| Apportioned LC Opening Charges (35%) | 16,975.00 |
| Insurance | 8,750.00 |
| Customs Duty (CD) | 195,750.63 |
| Regulatory Duty (RD) | 117,450.38 |
| C&F Charges | 15,750.00 |
| Inland Freight | 28,000.00 |
| **Total Shipment 2 Cost** | **4,250,176.01** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| Raw Material Inventory — Coal (Shipment 2) | 4,250,176.01 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC-in-Transit — LC 0123 / Shipment 2 | | 4,233,201.01 |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC-in-Transit — LC 0123 (LC-level apportioned) | | 16,975.00 |
| **Total** | **4,250,176.01** | **4,250,176.01** |

### GRN for Shipment 3 (Final)

| Cost Component | Amount (BDT) |
|---|---:|
| Goods Cost | 2,775,000.00 |
| Apportioned LC Opening Charges (remaining 25%) | 12,125.00 |
| Insurance | 6,250.00 |
| Customs Duty (CD) | 140,453.13 |
| Regulatory Duty (RD) | 84,271.88 |
| C&F Charges | 11,250.00 |
| Inland Freight | 20,000.00 |
| **Total Shipment 3 Cost** | **3,049,350.01** |

| Account | Debit (BDT) | Credit (BDT) |
|---|---:|---:|
| Raw Material Inventory — Coal (Shipment 3) | 3,049,350.01 | |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC-in-Transit — LC 0123 / Shipment 3 | | 3,037,225.01 |
| &nbsp;&nbsp;&nbsp;&nbsp;To LC-in-Transit — LC 0123 (LC-level apportioned) | | 12,125.00 |
| **Total** | **3,049,350.01** | **3,049,350.01** |

> **Final Check:** After all three GRNs, the parent `LC-in-Transit — LC No. 0123` (both shipment-level and LC-level sub-ledgers) balance must be **zero**. Any residual indicates a posting error or unallocated cost.

### Total Inventory Cost Across LC

| Shipment | Inventory Cost (BDT) |
|---|---:|
| Shipment 1 | 4,835,728.00 |
| Shipment 2 | 4,250,176.01 |
| Shipment 3 | 3,049,350.01 |
| **Total** | **12,135,254.02** |

---

## Apportionment Methods

For costs that arrive **at LC level** (like Event 2 — LC Opening Charges) — you must apportion across shipments. Two valid methods:

### Method 1: By Quantity (Physical Ratio)

Used when shipments contain the same product in different quantities.

```
Allocation to Shipment N = Total Cost × (Qty of Shipment N / Total Qty)
```

**Example:** LC Opening Charges of BDT 48,500 split by quantity (1,000 MT total coal):

| Shipment | Quantity (MT) | Ratio | Allocation (BDT) |
|---|---:|---:|---:|
| Shipment 1 | 400 | 0.40 | 19,400.00 |
| Shipment 2 | 350 | 0.35 | 16,975.00 |
| Shipment 3 | 250 | 0.25 | 12,125.00 |
| **Total** | **1,000** | **1.00** | **48,500.00** |

### Method 2: By Value (FC Ratio)

Used when shipments contain different products with different unit values.

```
Allocation to Shipment N = Total Cost × (FC Value of Shipment N / Total FC Value)
```

**Example:** LC Opening Charges of BDT 48,500 split by FC value:

| Shipment | FC Value (USD) | Ratio | Allocation (BDT) |
|---|---:|---:|---:|
| Shipment 1 | 40,000 | 0.40 | 19,400.00 |
| Shipment 2 | 35,000 | 0.35 | 16,975.00 |
| Shipment 3 | 25,000 | 0.25 | 12,125.00 |
| **Total** | **100,000** | **1.00** | **48,500.00** |

> **For homogeneous cargo like coal**, both methods give the same answer. For mixed-cargo LCs, **Method 1 (quantity)** is more accurate for inventory valuation per IAS 2.

---

## Critical ERP Design Considerations

### 1. Two-Level Sub-Ledger Structure

The `LC-in-Transit` account must support **two levels** of sub-ledger:

```
1305001  LC-in-Transit (Group)
├── LC No. 0123/IMP/26
│   ├── (LC-level)        ← holds Event 2 (LC Opening Charges)
│   ├── Shipment 1        ← holds Events 3.1, 4.1, 5.1, 6.1, 7.1
│   ├── Shipment 2        ← holds Events 3.2, 4.2, 5.2, 6.2, 7.2
│   └── Shipment 3        ← holds Events 3.3, 4.3, 5.3, 6.3, 7.3
└── LC No. 0124/IMP/26
    └── ...
```

### 2. PAD Accounts Per Shipment

Each retirement creates a separate PAD account (different maturity dates, different interest accrual periods). **Do not pool them** — bank reconciliation requires one-to-one match.

### 3. Rounding Discipline

When apportioning BDT 48,500 across three shipments, rounding can leave a 1–2 BDT residue. The **last shipment's GRN must close out the parent balance to zero**:

```python
def close_out_lc_balance(lc_id, final_shipment_id):
    """Final shipment absorbs any rounding residue."""
    parent_balance = get_lc_in_transit_balance(lc_id)
    shipment_costs = get_apportioned_costs(final_shipment_id)
    
    residue = parent_balance - shipment_costs.sum()
    if abs(residue) < ROUNDING_THRESHOLD_BDT:  # e.g., 5 BDT
        shipment_costs['rounding_adjustment'] = residue
    else:
        raise ValueError(
            f"Unallocated balance {residue} exceeds rounding threshold"
        )
    return shipment_costs
```

### 4. Maturity Tracking Per PAD

Each PAD has its own maturity date (typically 21–90 days from retirement). Build a dashboard:

| LC No. | Shipment | PAD Amount | Retirement Date | Maturity Date | Days Left |
|---|---|---:|---|---|---:|
| 0123/IMP/26 | Sh-1 | 3,524,000 | 2026-04-15 | 2026-07-14 | 65 |
| 0123/IMP/26 | Sh-2 | 3,101,000 | 2026-05-15 | 2026-08-13 | 95 |
| 0123/IMP/26 | Sh-3 | 2,227,500 | 2026-06-19 | 2026-09-17 | 130 |

### 5. Schema Additions

```sql
CREATE TABLE lc_shipment (
    shipment_id       INT IDENTITY PRIMARY KEY,
    lc_id             INT FOREIGN KEY REFERENCES lc_master(lc_id),
    shipment_no       VARCHAR(20),       -- 'SH-01', 'SH-02', 'SH-03'
    fc_amount         DECIMAL(18,2),     -- Per-shipment FC value
    qty               DECIMAL(18,4),     -- Physical quantity
    retirement_date   DATE NULL,
    retirement_rate   DECIMAL(10,4) NULL,
    grn_date          DATE NULL,
    status            VARCHAR(20),       -- 'PENDING','RETIRED','RECEIVED','CLOSED'
    pad_account       VARCHAR(20),       -- Sub-account for this shipment's PAD
    bl_no             VARCHAR(50),       -- Bill of Lading number
    be_no             VARCHAR(50),       -- Bill of Entry number
    created_at        DATETIME
);

CREATE TABLE lc_charges (
    charge_id         INT IDENTITY PRIMARY KEY,
    lc_id             INT FOREIGN KEY REFERENCES lc_master(lc_id),
    shipment_id       INT NULL FOREIGN KEY REFERENCES lc_shipment(shipment_id),
    event_type        VARCHAR(50),       -- 'MARGIN','OPENING','INSURANCE',
                                         -- 'RETIREMENT','CUSTOMS','CNF','FREIGHT'
    amount            DECIMAL(18,2),
    apportionment     VARCHAR(20),       -- 'DIRECT','BY_QTY','BY_VALUE'
    is_capitalizable  BIT,
    voucher_id        INT,
    charge_date       DATE
);
```

**Key design choice:** `shipment_id` is **nullable**. NULL means LC-level cost (needs apportionment); non-NULL means already shipment-specific.

### 6. Apportionment Logic (Django Example)

```python
from decimal import Decimal
from django.db.models import Sum

def apportion_lc_charges_to_shipments(lc_id, method='BY_VALUE'):
    """Distribute LC-level charges across shipments."""
    lc = LcMaster.objects.get(pk=lc_id)
    shipments = LcShipment.objects.filter(lc_id=lc_id).order_by('shipment_no')
    
    if method == 'BY_VALUE':
        total = sum(s.fc_amount for s in shipments)
        ratios = {s.shipment_id: s.fc_amount / total for s in shipments}
    elif method == 'BY_QTY':
        total = sum(s.qty for s in shipments)
        ratios = {s.shipment_id: s.qty / total for s in shipments}
    else:
        raise ValueError(f"Unknown apportionment method: {method}")
    
    # Get LC-level charges (shipment_id IS NULL)
    lc_charges = LcCharges.objects.filter(
        lc_id=lc_id,
        shipment_id__isnull=True,
        is_capitalizable=True
    )
    
    allocations = []
    for charge in lc_charges:
        for shipment_id, ratio in ratios.items():
            allocated = (charge.amount * ratio).quantize(Decimal('0.01'))
            allocations.append({
                'shipment_id': shipment_id,
                'charge_type': charge.event_type,
                'amount': allocated,
            })
    
    return allocations
```

---

## Event-Level Summary

| Event | Description | Scope | Frequency | Voucher Type |
|---|---|---|---|---|
| 1 | LC Margin Deposit | LC-level | Once at opening | BPV |
| 2 | LC Opening Charges | LC-level | Once at opening | BPV |
| 3 | Marine Insurance | Per shipment | Per shipment (N times) | BPV |
| 4 | Document Retirement | Per shipment | Per shipment (N times) | JV |
| 5 | Customs Duty, RD, VAT, AIT | Per shipment | Per B/E (N times) | BPV |
| 6 | C&F Agent Bill | Per shipment | Per shipment (N times) | JV |
| 7 | Inland Freight | Per shipment | Per shipment (N times) | JV |
| 8 | GRN — Transfer to Inventory | Per shipment | Per GRN (N times) | JV (auto) |

---

## Comparison — Single vs Multiple Shipment

| Aspect | Single Shipment | Multiple Shipments |
|---|---|---|
| Margin Deposit (Event 1) | 1 voucher | 1 voucher (same — LC-level) |
| LC Opening Charges (Event 2) | 1 voucher | 1 voucher (same — LC-level) |
| Insurance vouchers (Event 3) | 1 | N (per shipment policy) |
| Retirement vouchers (Event 4) | 1 | N (per shipment) |
| Customs vouchers (Event 5) | 1 | N (per B/E) |
| C&F vouchers (Event 6) | 1 | N (per shipment) |
| Freight vouchers (Event 7) | 1 | N (per shipment) |
| GRN vouchers (Event 8) | 1 | N (per shipment) |
| PAD accounts | 1 | N (one per shipment) |
| Exchange rates captured | 1 | N (may differ across shipments) |
| LC closure | At single GRN | After final GRN |
| Maturity tracking | 1 PAD maturity | N PAD maturities (staggered) |
| ERP complexity | Simple | Requires sub-shipment tracking |
| Audit complexity | Single trail | Per-shipment trail + LC-level reconciliation |

---

## Key Takeaways

**1. Events 1–2 stay at LC level.** Margin and opening charges happen once, regardless of shipment count. These get apportioned at GRN time.

**2. Events 3–8 happen per shipment.** Insurance, retirement, customs, C&F, freight, and GRN — each shipment generates its own complete set of vouchers.

**3. Each shipment is its own mini-LC.** Treat costs, retirement, and GRN per shipment, not per LC.

**4. Margin and PAD apportion strictly by FC value share.** This is non-negotiable — it must match the actual FC paid for each shipment.

**5. Exchange rates capture per retirement date.** Don't average. Each retirement uses the actual rate on that date.

**6. Apportion LC-level costs by quantity (preferred) or value.** Document the chosen method in policy; don't switch between LCs.

**7. The parent LC-in-Transit account must close to zero.** After the final shipment's GRN, run a reconciliation report. Any residue is a posting error.

**8. PAD maturities are independent.** Each PAD has its own clock — interest accrual, settlement instruction, and reconciliation are all per-PAD.

---

## License

This document is provided for educational and ERP-implementation reference. Adjust GL codes, tax rates, and exchange rates per your chart of accounts and the applicable NBR / Bangladesh Bank circular in effect at transaction date.
