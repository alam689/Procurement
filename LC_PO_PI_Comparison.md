
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

**Per Unit Cost** = 13,500,000 ÷ 5,000 = **2,700.00 BDT per bag**

---

## Stage 4 – LC Maturity Payment
```
Dr Bills Payable under LC         12,000,000
Dr Exchange Loss                     150,000
    Cr Bank Account                       12,150,000
```

---

# Accounts Position After LC Closure

## Assets
- Inventory: 13,500,000 BDT
- Input VAT Receivable: 1,800,000 BDT (if refundable)

## Liabilities
- Bills Payable: 0
- LC Margin: 0

## Expenses in P&L
- LC Opening Expense: 15,000
- Insurance Expense: 8,000
- Exchange Loss: 150,000

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
