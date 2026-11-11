# Demo data brief: Banking KYC/KYB + AML (Senzing demo truth set)

> **Status: draft for human review. Do not load until a reviewer approves it.**
> All records are synthetic. They come from Senzing's demo truth set (github.com/Senzing/truth-sets, `truthsets/demo`), and none of them are real people or companies.
> Prepared Nov 9, 2026.

## Scenario

A fictional US bank wants one trusted view of every customer. That way the people and AI agents who serve customers know who they're dealing with and who each customer is connected to, and the bank can meet its KYC, KYB and AML duties. The demo tells the story in three parts. The examples come from the truth set's answer key and should be confirmed on load.

**1. Customer 360 (KYC): one view of each customer.** The customer records (`CUSTOMERS`, 120 records) hold the same people and businesses many times over, under nicknames, misspellings, other alphabets, old addresses and partial IDs. Senzing resolves them into single customers, each with every account in one place. For example, six records for Susan Mooney, spelled five different ways, become one customer. Senzing also keeps apart people who only look alike.

**2. Who they're connected to (KYB).** A business registry (`REFERENCE`, 22 records) adds each business's owners (40% to 100%), directors, principals and parent companies, and links them to the resolved customers. For example, the Platinum business customer "Mullenkrants" is the registered partnership Müllenkranz, which is 100% owned by Aleksandr Vasiliev and has Faisal Siddiqui as president. The business customers "Univrsl Export Inc", "Singapore exports" and "India Exports" turn out to belong to one corporate family, Universal Exports.

**3. AML screening.** The resolved customers and their owners are screened against a watchlist (`WATCHLIST`, 17 records) of sanctioned people, politically exposed persons (PEPs) and people the bank has flagged for fraud. This catches matches a name-only check misses. For example, Vasiliev is also a Platinum customer under the spelling "Alexander Pavlovich Vasiliev", and he is on the sanctions list in Cyrillic script; Siddiqui's sanctions listing is in Arabic script. Margaret Charney, a 50% owner of Universal Exports Worldwide, is a PEP listed as "Peggie Charney". Edward Kusha, whose account was once closed under "Ed Knight", is on the internal fraud list under both names. Robert Smith, a different customer, is kept apart from Robert Smith Sr on that list. Each match is shown as a lead with evidence, not a verdict.

## What to do after approval

1. Apply the labels below. Change labels only. Keep every record value, `RECORD_ID` and Senzing attribute name as it is.
2. Load the three files into a fresh Senzing instance through the Senzing MCP server.
3. Compare the results with `actual_truthset_key.csv` and report any differences. Present relationships and watchlist hits as results found on load, not as expected facts.

## Label mapping

### Data sources

| Existing | New | Role in the story |
|---|---|---|
| `CUSTOMERS` | `BANK_CUSTOMERS` | The bank's customer and account records, for people and businesses |
| `REFERENCE` | `BUSINESS_REGISTRY` | Third-party company and beneficial-ownership data used for KYB |
| `WATCHLIST` | `AML_WATCHLIST` | Sanctions, PEP and internal fraud lists used for screening |

### Record fields (not used for matching)

| Existing | `BANK_CUSTOMERS` | `BUSINESS_REGISTRY` | `AML_WATCHLIST` |
|---|---|---|---|
| `AMOUNT` | Account balance | — | — |
| `DATE` | Account open date | Registration date | Date listed |
| `STATUS` | Account status (shows "Terminated" as "Closed by bank") | Registration status | Listing status |
| `CATEGORY` | Customer tier | Party type (Owner, Director, Contact, Corporation and so on) | List type (shows "Fraud" as "Internal fraud list") |

### Display labels for matching attributes (on screen only; attribute names unchanged)

| Senzing attribute(s) | Label |
|---|---|
| `NAME_FIRST`, `NAME_MIDDLE`, `NAME_LAST`, `NAME_FULL` | Customer name (in `BUSINESS_REGISTRY`: Owner or officer name; in `AML_WATCHLIST`: Listed name) |
| `NAME_ORG` | Business name |
| `NAME_TYPE` = AKA | Also known as |
| `RECORD_TYPE` | Customer type (Person or Business) |
| `DATE_OF_BIRTH`, `GENDER` | Date of birth, Gender |
| `ADDR_*` | Address (with its type: Home, Mailing, Registered and so on) |
| `PHONE_NUMBER`, `EMAIL_ADDRESS` | Phone, Email |
| `SSN_NUMBER`, `DRIVERS_LICENSE_*`, `PASSPORT_*`, `NATIONAL_ID_*` | SSN, Driver's license, Passport, National ID (KYC documents) |
| `TAX_ID_*`, `LEI_NUMBER` | Business tax ID (EIN), Legal Entity Identifier |
| `EMPLOYER` | Employer |
| `REL_POINTER_ROLE` | Ownership or control (for example "Owns 60%", "Principal", "Global Parent") |

## Notes for reviewers

- One generic email address (`info@ca-state.gov`) appears on records for 19 different entities. Treat it as a shared contact address, not a link between them, and check that the domain doesn't belong to a real organization before showing it.
- One address is shared by 5 entities and one email by 4. These may make good "connected or not?" examples, depending on what Senzing reports on load.
- The business registry includes a co-owner named Howard Hughes, which is also the name of a well-known real person. Don't feature that record on stage; use Margaret Charney for the PEP example.
