
# LC Accounting Example with Per-Unit Costing

## Scenario
- **LC Open Date:** 1-Aug-2025  
- **LC Value:** USD 100,000  
- **Quantity:** 5,000 bags  
- **LC Type:** Deferred (90 days from BL date) — Not Sight LC  
- **Exchange Rate at LC opening:** 1 USD = 120.00 BDT  
- **Exchange Rate at payment:** 1 USD = 121.50 BDT  
- **LC Margin:** 10% of LC value  
- **Bank LC Opening Charges:** 15,000 BDT  
- **Insurance Premium:** 8,000 BDT  
- **Freight & Other Costs (paid at arrival):** 300,000 BDT  
- **Customs Duty:** 10% of CIF value  
- **VAT on Import:** 15% of CIF value  

---

## Stage 1 – LC Opening (1-Aug)
**LC Value in BDT:** 100,000 × 120 = **12,000,000 BDT**  
**LC Margin (10%):** 1,200,000 BDT

```
Dr LC Margin with Bank       1,200,000
    Cr Bank Account                  1,200,000

Dr LC Opening Expense           15,000
    Cr Bank Account                     15,000

Dr Insurance Expense             8,000
    Cr Bank Account                      8,000
```

---

## Stage 2 – Shipment / Documents Presented
```
Dr Goods in Transit (Inventory)     12,000,000
    Cr Bills Payable under LC             12,000,000
```

---

## Stage 3 – Goods Arrival & Customs
**CIF Value:** 12,000,000  
**Customs Duty (10%):** 1,200,000  
**VAT (15%):** 1,800,000

```
Dr Inventory (CIF value)           12,000,000
    Cr Goods in Transit                  12,000,000

Dr Inventory (Customs Duty)        1,200,000
Dr Input VAT Receivable             1,800,000
    Cr Bank Account                       3,000,000

Dr Inventory                        300,000
    Cr Bank Account                      300,000
```

📌 Total Inventory Cost =
12,000,000 (CIF) + 1,200,000 (Duty) + 300,000 (Freight)
= 13,500,000 BDT

Per Unit Cost = 13,500,000 ÷ 5,000 = 2,700.00 BDT per bag
(VAT not included in cost if refundable)

---

## Stage 4 – LC Maturity Payment
```
Dr Bills Payable under LC         12,000,000
Dr Exchange Loss                     150,000
    Cr Bank Account                       12,150,000
```

---
Final Summary Table – LC Accounting Flow (with numbers)
| Stage         | Dr (Debit)           | Cr (Credit)      | Amount (BDT) | Notes                  |
| ------------- | -------------------- | ---------------- | ------------ | ---------------------- |
| LC Opening    | LC Margin            | Bank             | 1,200,000    | 10% margin deposit     |
|               | LC Opening Expense   | Bank             | 15,000       | Bank charges           |
|               | Insurance Expense    | Bank             | 8,000        | Shipment insurance     |
| Shipment Date | Goods in Transit     | Bills Payable    | 12,000,000   | CIF value              |
| Arrival       | Inventory            | Goods in Transit | 12,000,000   | Transfer stock         |
|               | Inventory + VAT      | Bank             | 3,000,000    | Duty + VAT             |
|               | Inventory            | Bank             | 300,000      | Freight, clearing      |
| Payment Date  | Bills Payable + Loss | Bank             | 12,150,000   | Payment at new FX rate |


# 1️⃣ Accounts Position After Closing LC
Once all stages are complete (goods received, all expenses booked, LC matured & paid), here’s what remains:

## Assets
Inventory: Increased by CIF value + Duty + Freight + other capitalized costs.
In our numeric example:
Inventory = 12,000,000 + 1,200,000 + 300,000 = 13,500,000 BDT
- Inventory: 13,500,000 BDT
- Input VAT Receivable: 1,800,000 BDT (if refundable)
- LC Margin with Bank: 0 — it’s returned or adjusted upon LC settlement.

## Liabilities
Bills Payable under LC: 0 — fully paid at maturity.
Creditors (if any clearing agent still unpaid): Could have a small payable if not yet settled.
- Bills Payable: 0
- LC Margin: 0

## Expenses in P&L
- LC Opening Charges: 15,000 (P&L – administrative/financial expense).
- Insurance Expense: 8,000 (P&L – if not capitalized).
- Exchange Loss: 150,000 (P&L – due to FX rate increase at payment date).

# 2️⃣ LC Opening Charges – Accounting Treatment

These are non-refundable bank costs for opening the LC (bank commission, SWIFT charges, stamp duty).

Normally treated as Finance Cost or Bank Charges in Profit & Loss.

Do not capitalize to inventory unless your company policy treats them as directly attributable to acquisition (some IFRS-compliant policies allow this if directly related to bringing inventory to present location).

In our example:
Dr LC Opening Expense (P&L)   15,000
    Cr Bank Account                15,000
This stays in the expense account and reduces profit — it’s not in the final inventory value.
# 3️⃣ Final Snapshot (Example in BDT)
| Account                | Debit      | Credit         | Balance      |
| ---------------------- | ---------- | -------------- | ------------ |
| Inventory              | 13,500,000 | –              | 13,500,000   |
| Input VAT Receivable   | 1,800,000  | –              | 1,800,000    |
| Bank                   | –          | (17,673,000)\* | (17,673,000) |
| LC Opening Expense     | 15,000     | –              | 15,000       |
| Insurance Expense      | 8,000      | –              | 8,000        |
| Exchange Loss          | 150,000    | –              | 150,000      |
| LC Margin with Bank    | –          | –              | 0            |
| Bills Payable under LC | –          | –              | 0            |
* Bank outflow = Margin (1,200,000) + LC opening (15,000) + Insurance (8,000) + Customs/Duty/VAT (3,000,000) + Freight (300,000) + LC settlement at maturity (12,150,000)
# 4️⃣ Key Points

LC opening charges = Directly expensed (Finance/Bank Charges) unless company policy capitalizes them.

After LC closure, the only major asset impact is Inventory (and VAT receivable if applicable).

All LC-related liabilities are cleared; P&L reflects only the non-capitalized charges and exchange differences.

---

# Purchase Order vs Proforma Invoice

| Feature | Purchase Order (PO) | Proforma Invoice (PI) |
|---------|--------------------|-----------------------|
| **Issuer** | Buyer | Seller |
| **Purpose** | Order confirmation to supplier | Price & terms quotation |
| **Binding** | Yes, once accepted | No |
| **Payment Terms** | Yes | Yes |
| **Used for Customs** | No | Yes |
| **Used for LC Opening** | Rarely | Often |

**Flow Example:**
1. Supplier sends Proforma Invoice → Buyer reviews.
2. Buyer issues Purchase Order → Supplier accepts.
3. Supplier ships goods & issues Commercial Invoice.

---
