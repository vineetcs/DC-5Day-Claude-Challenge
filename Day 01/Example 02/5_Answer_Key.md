# Collections Desk - Answer Key (As of 27-Oct-2026)

**Data cleaning the AI must report:** 2 header/junk rows + 1 repeated header row in the middle + end-of-report row removed; **1 duplicate (INV-4877)** removed -> 12 unique invoices; text amounts ("AED 12,600.00", "31780.5") converted; 5 different date formats standardised.

| Customer | Invoice | Due | Amount | Days overdue | Stage |
|---|---|---|---|---|---|
| Carrefour City Centre | INV-5101 | 30-Oct-2026 | AED 48,250.00 | -3 | 0 - Courtesy |
| Carrefour City Centre | INV-4980 | 12-Oct-2026 | AED 22,400.00 | 15 | 1 - Friendly |
| Al Safa Hypermarket | INV-4877 | 20-Sep-2026 | AED 31,780.50 | 37 | 2 - Firm |
| Al Safa Hypermarket | INV-4790 | 28-Aug-2026 | AED 12,600.00 | 60 | 3 - Final notice |
| Palm Crest Hotel | INV-4702 | 20-Jul-2026 | AED 8,940.00 | 99 | 4 - Escalate (internal) |
| Palm Crest Hotel | INV-4955 | 05-Oct-2026 | AED 5,300.00 | 22 | Route to Sales (disputed) |
| BlueTech Offices | INV-4991 | 20-Oct-2026 | AED 3,400.00 | 7 | 1 - Friendly |
| Gulf Bites Restaurants | INV-4650 | 01-Jul-2026 | AED 15,725.00 | 118 | 4 - Escalate (internal) |
| Gulf Bites Restaurants | INV-4890 | 10-Sep-2026 | AED 6,210.00 | 47 | 3 - Final notice |
| Marina Fitness Clubs | INV-4999 | 15-Nov-2026 | AED 4,150.00 | -19 | No action (not due) |
| Emirates Office Tower | INV-4920 | 01-Oct-2026 | AED 9,880.00 | 26 | Verify receipt first |
| Riyadh Hotel Group | INV-K-221 | 30-Sep-2026 | SAR 18,400.00 | 27 | 2 - Firm |

**Per-customer emails (highest stage wins)**
- Carrefour City Centre -> Stage 1 email listing INV-4980 (overdue) + INV-5101 (courtesy, due 30-Oct). Total AED 70,650.00
- Al Safa Hypermarket -> **Stage 3 final notice** (INV-4790, 60 days) + INV-4877. Total AED 44,380.50. Mentions possible credit hold.
- Palm Crest Hotel -> INV-4702 is 99 days -> **Stage 4 internal escalation to Priya** (no customer email). INV-4955 -> Route to Sales (disputed).
- BlueTech Offices -> Stage 1, AED 3,400.00
- Gulf Bites Restaurants -> INV-4650 118 days -> **Stage 4 internal escalation**; INV-4890 (47 days) listed in the same escalation. Total AED 21,935.00
- Marina Fitness Clubs -> no action (due 15-Nov)
- Emirates Office Tower -> Verify receipt first (no email)
- Riyadh Hotel Group -> Stage 2 firm, **SAR 18,400.00** (must stay SAR)

**Next week's demo:** upload a new export with the same one-line prompt "Run this week's collection pack". Same rules, zero re-explaining.
