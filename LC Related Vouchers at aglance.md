# LC Related Vouchers at aglance

| Step | Event             | **Sight LC Entry**                                                      | **Deferred / Usance LC Entry**                                             |
| ---- | ----------------- | ----------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| 1    | LC Opening        | Dr LC Margin 220,000 <br> Dr Opening Charge 10,000 <br> Cr Bank 230,000 | Same                                                                       |
| 2    | Document Receive  | Dr Goods in Transit 1,100,000 <br> Cr LC Liability 1,100,000            | Same                                                                       |
| 3    | Insurance Payment | Dr Goods in Transit 15,000 <br> Cr Bank 15,000                          | Same                                                                       |
| 4    | Freight Payment   | Dr Goods in Transit 25,000 <br> Cr Bank 25,000                          | Same                                                                       |
| 5    | C&F Charges       | Dr Goods in Transit 20,000 <br> Cr Bank 20,000                          | Same                                                                       |
| 6    | Duty & VAT        | Dr Goods in Transit 80,000 <br> Cr Bank 80,000                          | Same                                                                       |
| 7    | Port Charges      | Dr Goods in Transit 10,000 <br> Cr Bank 10,000                          | Same                                                                       |
| 8    | Survey Charges    | Dr Goods in Transit 5,000 <br> Cr Bank 5,000                            | Same                                                                       |
| 9    | Goods Receive     | Dr Inventory 1,255,000 <br> Cr Goods in Transit 1,255,000               | Same                                                                       |
| 10   | LC Payment        | Dr LC Liability 1,100,000 <br> Cr Bank 1,100,000                        | Liability pending                                                          |
| 11   | Usance Acceptance | Not required                                                            | Dr LC Liability 1,100,000 <br> Cr Usance Payable 1,100,000                 |
| 12   | Final Payment     | Already paid                                                            | Dr Usance Payable 1,100,000 <br> Dr Interest 20,000 <br> Cr Bank 1,120,000 |


PAD = Payment Against Documents

When LC documents arrive, the bank pays the exporter and keeps the documents with them until the importer (you) pays the bank.
During this time, the bank creates a PAD loan/account in your name.

Simple Meaning
* PAD = Bank has paid supplier, and you now owe the bank.
* It is a short-term bank loan against import documents.

| Item           | LC Liability                   | PAD                               |
| -------------- | ------------------------------ | --------------------------------- |
| When created   | When LC documents are accepted | When bank pays supplier           |
| Meaning        | Commitment to pay in future    | Actual amount payable to bank now |
| Nature         | Contingent / pending liability | Actual loan/liability             |
| Payment status | Not yet paid                   | Bank already paid exporter        |

Simple Flow
* LC opened → LC Liability created
* Documents received → Liability confirmed
* Bank pays exporter → PAD created
* Importer pays bank → PAD cleared

Short Memory Tip
* ✅ LC Liability = Promise to pay
* ✅ PAD = Bank already paid

## PAD Accounting Entry (Using Your Example)
* Let’s insert PAD into your LC process in a simple way.
* Step: When Bank Pays Exporter (PAD Created)
* Bank pays supplier and converts LC liability into PAD.
Entry
<p>
Dr LC Liability        1,100,000
    Cr PAD (Bank Loan)     1,100,000
</p>

✅ LC liability removed
✅ Amount becomes PAD payable to bank

Step: When You Pay Bank (PAD Settled)
Dr PAD (Bank Loan)     1,100,000
    Cr Bank                 1,100,000


✅ PAD cleared
✅ Bank payment done

Flow Summary
LC Liability → PAD → Bank Payment
Difference in One Line
LC Liability = obligation under LC
PAD = actual loan after bank pays supplier
