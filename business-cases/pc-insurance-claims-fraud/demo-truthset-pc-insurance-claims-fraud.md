# Demo data brief: P&C Insurance + Claims Fraud (Senzing demo truth set)

> **Status: draft for human review. Do not load until a reviewer approves it.**
> All records are synthetic. They come from Senzing's demo truth set (github.com/Senzing/truth-sets, `truthsets/demo`), and none of them are real people or companies.
> Prepared Nov 9, 2026.

## Scenario

A fictional US property and casualty insurer wants one trusted view of every policyholder. That way the people and AI agents who serve customers know who they're dealing with and who each person is connected to, and the insurer can pay honest claims quickly and stop fraudulent ones. The demo tells the story in three parts. The examples come from the truth set's answer key and should be confirmed on load.

**1. Customer 360: one view of each policyholder.** The policy and claims records (`CUSTOMERS`, 120 records) hold the same people many times over, under nicknames, misspellings, other alphabets, old addresses and partial IDs. Senzing resolves them into single policyholders, each with one full policy and claim history. For example, six records for Susan Mooney, spelled four different ways, become one policyholder. Senzing also keeps apart people who only look alike.

**2. Who they're connected to.** A vendor registry (`REFERENCE`, 22 records) covers the repair shops, medical providers and other businesses paid on claims, including their owners, directors and parent companies. Linked to the resolved policyholders, it shows when a claimant owns part of a business that profits from a claim. For example, policyholders Jie Wang and Wei Wang, each also on file in Chinese characters, own 60% and 40% of the vendor Hajah Maimunah.

**3. Fraud reveal.** The resolved policyholders and vendor owners are checked against the special investigations unit's (SIU) watchlist (`WATCHLIST`, 17 records). It lists known claims-fraud suspects, plus the sanctioned people and politically exposed persons (PEPs) every insurer must screen for. For example, Edward Kusha has an active policy, had one cancelled as "Ed Knight", and is on the SIU list as both Eddie Kusha and Eddie Knight. Robert Smith, a different policyholder, is kept apart from Robert Smith Sr on the list. Each match is shown as a lead for the SIU with evidence, not a verdict.

## What to do after approval

1. Apply the labels below, and remove the `AMOUNT` field from every record. It isn't used for matching or in the story, so the answer key still applies. Otherwise change labels only: keep every other record value, `RECORD_ID` and Senzing attribute name as it is.
2. Load the three files into a fresh Senzing instance through the Senzing MCP server.
3. Compare the results with `actual_truthset_key.csv` and report any differences. Present relationships and watchlist hits as results found on load, not as expected facts.

## Label mapping

### Data sources

| Existing | New | Role in the story |
|---|---|---|
| `CUSTOMERS` | `POLICYHOLDERS` | The insurer's policy and claims records, for people and businesses |
| `REFERENCE` | `VENDOR_REGISTRY` | Third-party data on businesses paid on claims, with their owners and parents |
| `WATCHLIST` | `SIU_WATCHLIST` | Known claims-fraud suspects, plus the sanctions and PEP lists |

### Record fields (not used for matching)

| Existing | `POLICYHOLDERS` | `VENDOR_REGISTRY` | `SIU_WATCHLIST` |
|---|---|---|---|
| `DATE` | Policy effective date | Registration date | Date listed |
| `STATUS` | Policy status (shows "Terminated" as "Cancelled") | License status | Listing status |
| `CATEGORY` | Coverage tier | Party type (Owner, Director, Contact, Corporation and so on) | List type (shows "Fraud" as "Known claims fraud") |

### Display labels for matching attributes (on screen only; attribute names unchanged)

| Senzing attribute(s) | Label |
|---|---|
| `NAME_FIRST`, `NAME_MIDDLE`, `NAME_LAST`, `NAME_FULL` | Policyholder name (in `VENDOR_REGISTRY`: Owner or officer name; in `SIU_WATCHLIST`: Listed name) |
| `NAME_ORG` | Business name (in `VENDOR_REGISTRY`: Vendor name) |
| `NAME_TYPE` = AKA | Also known as |
| `RECORD_TYPE` | Party type (Person or Business) |
| `DATE_OF_BIRTH`, `GENDER` | Date of birth, Gender |
| `ADDR_*` | Address (with its type: Home, Mailing, Registered and so on) |
| `PHONE_NUMBER`, `EMAIL_ADDRESS` | Phone, Email |
| `SSN_NUMBER`, `PASSPORT_*`, `NATIONAL_ID_*` | SSN, Passport, National ID |
| `DRIVERS_LICENSE_*` | Driver's license (listed driver) |
| `TAX_ID_*`, `LEI_NUMBER` | Business tax ID (EIN), Legal Entity Identifier |
| `EMPLOYER` | Employer |
| `REL_POINTER_ROLE` | Ownership or control (for example "Owns 60%", "Principal", "Global Parent") |

## Notes for reviewers

- One generic email address (`info@ca-state.gov`) appears on records for 19 different entities. Treat it as a shared contact address, not a ring, and check that the domain doesn't belong to a real organization before showing it.
- One address is shared by 5 entities and one email by 4. These may make good "ring or household?" examples, depending on what Senzing reports on load.
- The vendor registry includes a co-owner named Howard Hughes, which is also the name of a well-known real person. Don't feature that record on stage.
