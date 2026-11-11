# Demo data brief: P&C Insurance + Claims Fraud (Senzing demo truth set)

> **Status: draft for human review. Do not load until a reviewer approves it.**
> **Source data:** every record comes from the Senzing demo truth set at <https://github.com/Senzing/truth-sets/tree/main/truthsets/demo>. All records are synthetic, and none of them are real people or companies.
> This brief renames the truth set's metadata (data-source names, CSV column names and JSON keys) to fit the story. It never changes a data value.
> Prepared Nov 9, 2026.

## Scenario

A fictional US property and casualty insurer wants one trusted view of every policyholder. That way the people and AI agents who serve customers know who they're dealing with and who each person is connected to, and the insurer can pay honest claims quickly and stop fraudulent ones. The demo tells the story in three parts. The examples come from the truth set's answer key and should be confirmed on load.

**1. Customer 360: one view of each policyholder.** The policy and claims records (`POLICYHOLDERS`, 120 records) hold the same people many times over, under nicknames, misspellings, other alphabets, old addresses and partial IDs. Senzing resolves them into single policyholders, each with one full policy and claim history. For example, six records for Susan Mooney, spelled four different ways, become one policyholder. Senzing also keeps apart people who only look alike.

**2. Who they're connected to.** A vendor registry (`VENDOR_REGISTRY`, 22 records) covers the repair shops, medical providers and other businesses paid on claims, including their owners, directors and parent companies. Linked to the resolved policyholders, it shows when a claimant owns part of a business that profits from a claim. For example, policyholders Jie Wang and Wei Wang, each also on file in Chinese characters, own 60% and 40% of the vendor Hajah Maimunah.

**3. Fraud reveal.** The resolved policyholders and vendor owners are checked against the special investigations unit's (SIU) watchlist (`SIU_WATCHLIST`, 17 records). It lists known claims-fraud suspects, plus the sanctioned people and politically exposed persons (PEPs) every insurer must screen for. For example, Edward Kusha has an active policy, had one cancelled as "Ed Knight", and is on the SIU list as both Eddie Kusha and Eddie Knight. Robert Smith, a different policyholder, is kept apart from Robert Smith Sr on the list. Each match is shown as a lead for the SIU with evidence, not a verdict.

## What to do after approval

1. Download the source files from the [truth set](https://github.com/Senzing/truth-sets/tree/main/truthsets/demo): `customers`, `reference` and `watchlist` (as `.csv` or `.jsonl`), plus `actual_truthset_key.csv`, `alternate_truthset_key.csv` and `truthset_config.g2c`.
2. Rename the three data sources as shown in [Data sources](#data-sources), in the record files, in both answer keys and in `truthset_config.g2c`.
3. Rename every field as shown in [Field names](#field-names). Drop the `AMOUNT` field, which isn't used for matching or in the story. Change no data value.
4. Map each story name back to the Senzing attribute in its table, then load the three files into a fresh Senzing instance through the Senzing MCP server.
5. Compare the results with the renamed `actual_truthset_key.csv` and report any differences. Present relationships and watchlist hits as results found on load, not as expected facts.

## Metadata mapping

### Data sources

| Existing | New | Role in the story |
|---|---|---|
| `CUSTOMERS` | `POLICYHOLDERS` | The insurer's policy and claims records, for people and businesses |
| `REFERENCE` | `VENDOR_REGISTRY` | Third-party data on businesses paid on claims, with their owners and parents |
| `WATCHLIST` | `SIU_WATCHLIST` | Known claims-fraud suspects, plus the sanctions and PEP lists |

Apply these names everywhere the old ones appear: the `DATA_SOURCE` value of every record, the `DATA_SOURCE` column of `actual_truthset_key.csv` and `alternate_truthset_key.csv`, and the `addDataSource` lines in `truthset_config.g2c`.

### Rules for every rename

- **Rename metadata only.** Apply the data-source names and the field names below. Keep every data value exactly as it is: names, dates, addresses, identifiers, phone numbers, emails, relationship keys, record IDs and every other value. The truth set is tuned so that each value illustrates an entity-resolution principle, and `actual_truthset_key.csv` depends on those values.
- **Keep `DATA_SOURCE` and `RECORD_ID` as key names.** Only the data-source *values* change.
- **Keep the JSON structure.** In the JSONL files, rename keys in place, both the top-level keys and the keys inside each record's `FEATURES` array.
- **Map back exactly.** Before loading, map each story name back to the Senzing attribute in its table's last column. Don't infer a mapping from the story names.
- Fields marked "payload, not used for matching" load as ordinary record fields under their story names.

### Field names

Each table lists every key in that data source's CSV and JSONL files, except `DATA_SOURCE` and `RECORD_ID`. "Found in" says which files have the key: `CSV` for the CSV header, `JSON` for a top-level JSONL key, and `JSON FEATURES` for a key inside `FEATURES`.

#### `POLICYHOLDERS` (from `customers.csv` and `customers.jsonl`)

| Truth-set key | Found in | Story name | Senzing attribute on load |
| --- | --- | --- | --- |
| `PRIMARY_NAME_FULL` | CSV | `POLICYHOLDER_FULL_NAME` | `PRIMARY_NAME_FULL` |
| `PRIMARY_NAME_ORG` | CSV | `POLICYHOLDER_BUSINESS_NAME` | `PRIMARY_NAME_ORG` |
| `PRIMARY_NAME_LAST` | CSV | `POLICYHOLDER_LAST_NAME` | `PRIMARY_NAME_LAST` |
| `PRIMARY_NAME_FIRST` | CSV | `POLICYHOLDER_FIRST_NAME` | `PRIMARY_NAME_FIRST` |
| `PRIMARY_NAME_MIDDLE` | CSV | `POLICYHOLDER_MIDDLE_NAME` | `PRIMARY_NAME_MIDDLE` |
| `NATIVE_NAME_FULL` | CSV | `POLICYHOLDER_NAME_NATIVE_SCRIPT` | `NATIVE_NAME_FULL` |
| `SECONDARY_NAME_ORG` | CSV | `POLICYHOLDER_BUSINESS_NAME_ALT` | `SECONDARY_NAME_ORG` |
| `GENDER` | CSV, JSON `FEATURES` | `POLICYHOLDER_GENDER` | `GENDER` |
| `DATE_OF_BIRTH` | CSV, JSON `FEATURES` | `POLICYHOLDER_BIRTH_DATE` | `DATE_OF_BIRTH` |
| `PASSPORT_NUMBER` | CSV, JSON `FEATURES` | `POLICYHOLDER_PASSPORT_NO` | `PASSPORT_NUMBER` |
| `PASSPORT_COUNTRY` | CSV, JSON `FEATURES` | `POLICYHOLDER_PASSPORT_ISSUER` | `PASSPORT_COUNTRY` |
| `DRIVERS_LICENSE_NUMBER` | CSV, JSON `FEATURES` | `POLICYHOLDER_DRIVER_LICENSE_NO` | `DRIVERS_LICENSE_NUMBER` |
| `DRIVERS_LICENSE_STATE` | CSV, JSON `FEATURES` | `POLICYHOLDER_DRIVER_LICENSE_ISSUER` | `DRIVERS_LICENSE_STATE` |
| `SSN_NUMBER` | CSV, JSON `FEATURES` | `POLICYHOLDER_SSN` | `SSN_NUMBER` |
| `NATIONAL_ID_NUMBER` | CSV, JSON `FEATURES` | `POLICYHOLDER_NATIONAL_ID_NO` | `NATIONAL_ID_NUMBER` |
| `NATIONAL_ID_COUNTRY` | CSV, JSON `FEATURES` | `POLICYHOLDER_NATIONAL_ID_ISSUER` | `NATIONAL_ID_COUNTRY` |
| `EMAIL_ADDRESS` | CSV, JSON `FEATURES` | `POLICYHOLDER_EMAIL` | `EMAIL_ADDRESS` |
| `ADDR_FULL` | CSV, JSON `FEATURES` | `POLICYHOLDER_ADDRESS` | `ADDR_FULL` |
| `ADDR_LINE1` | CSV, JSON `FEATURES` | `POLICYHOLDER_STREET` | `ADDR_LINE1` |
| `ADDR_CITY` | CSV, JSON `FEATURES` | `POLICYHOLDER_CITY` | `ADDR_CITY` |
| `ADDR_STATE` | CSV, JSON `FEATURES` | `POLICYHOLDER_STATE` | `ADDR_STATE` |
| `ADDR_POSTAL_CODE` | CSV, JSON `FEATURES` | `POLICYHOLDER_POSTAL_CODE` | `ADDR_POSTAL_CODE` |
| `ADDR_COUNTRY` | CSV, JSON `FEATURES` | `POLICYHOLDER_COUNTRY` | `ADDR_COUNTRY` |
| `ADDR_TYPE` | CSV, JSON `FEATURES` | `POLICYHOLDER_ADDRESS_KIND` | `ADDR_TYPE` |
| `PHONE_NUMBER` | CSV, JSON `FEATURES` | `POLICYHOLDER_PHONE` | `PHONE_NUMBER` |
| `PHONE_TYPE` | CSV, JSON `FEATURES` | `POLICYHOLDER_PHONE_KIND` | `PHONE_TYPE` |
| `RECORD_TYPE` | CSV, JSON `FEATURES` | `POLICYHOLDER_PARTY_TYPE` | `RECORD_TYPE` |
| `AMOUNT` | CSV, JSON | *(drop this field)* | none |
| `CATEGORY` | CSV, JSON | `COVERAGE_TIER` | none (payload, not used for matching) |
| `DATE` | CSV, JSON | `POLICY_EFFECTIVE_DATE` | none (payload, not used for matching) |
| `STATUS` | CSV, JSON | `POLICY_STATUS` | none (payload, not used for matching) |
| `NAME_LAST` | JSON `FEATURES` | `POLICYHOLDER_LAST_NAME` | `NAME_LAST` |
| `NAME_FIRST` | JSON `FEATURES` | `POLICYHOLDER_FIRST_NAME` | `NAME_FIRST` |
| `NAME_MIDDLE` | JSON `FEATURES` | `POLICYHOLDER_MIDDLE_NAME` | `NAME_MIDDLE` |
| `NAME_FULL` | JSON `FEATURES` | `POLICYHOLDER_FULL_NAME` | `NAME_FULL` |
| `NAME_ORG` | JSON `FEATURES` | `POLICYHOLDER_BUSINESS_NAME` | `NAME_ORG` |
| `NAME_TYPE` | JSON `FEATURES` | `POLICYHOLDER_NAME_KIND` | `NAME_TYPE` |

#### `VENDOR_REGISTRY` (from `reference.csv` and `reference.jsonl`)

| Truth-set key | Found in | Story name | Senzing attribute on load |
| --- | --- | --- | --- |
| `PRIMARY_NAME_FULL` | CSV | `VENDOR_FULL_NAME` | `PRIMARY_NAME_FULL` |
| `PRIMARY_NAME_ORG` | CSV | `VENDOR_BUSINESS_NAME` | `PRIMARY_NAME_ORG` |
| `PRIMARY_NAME_LAST` | CSV | `VENDOR_LAST_NAME` | `PRIMARY_NAME_LAST` |
| `PRIMARY_NAME_FIRST` | CSV | `VENDOR_FIRST_NAME` | `PRIMARY_NAME_FIRST` |
| `NATIVE_NAME_FULL` | CSV | `VENDOR_NAME_NATIVE_SCRIPT` | `NATIVE_NAME_FULL` |
| `SECONDARY_NAME_ORG` | CSV | `VENDOR_BUSINESS_NAME_ALT` | `SECONDARY_NAME_ORG` |
| `DATE_OF_BIRTH` | CSV, JSON `FEATURES` | `VENDOR_BIRTH_DATE` | `DATE_OF_BIRTH` |
| `EMAIL_ADDRESS` | CSV, JSON `FEATURES` | `VENDOR_EMAIL` | `EMAIL_ADDRESS` |
| `ADDR_FULL` | CSV, JSON `FEATURES` | `VENDOR_ADDRESS` | `ADDR_FULL` |
| `ADDR_LINE1` | CSV, JSON `FEATURES` | `VENDOR_STREET` | `ADDR_LINE1` |
| `ADDR_CITY` | CSV, JSON `FEATURES` | `VENDOR_CITY` | `ADDR_CITY` |
| `ADDR_STATE` | CSV, JSON `FEATURES` | `VENDOR_STATE` | `ADDR_STATE` |
| `ADDR_POSTAL_CODE` | CSV, JSON `FEATURES` | `VENDOR_POSTAL_CODE` | `ADDR_POSTAL_CODE` |
| `ADDR_COUNTRY` | CSV, JSON `FEATURES` | `VENDOR_COUNTRY` | `ADDR_COUNTRY` |
| `ADDR_TYPE` | CSV, JSON `FEATURES` | `VENDOR_ADDRESS_KIND` | `ADDR_TYPE` |
| `PHONE_NUMBER` | CSV, JSON `FEATURES` | `VENDOR_PHONE` | `PHONE_NUMBER` |
| `PHONE_TYPE` | CSV, JSON `FEATURES` | `VENDOR_PHONE_KIND` | `PHONE_TYPE` |
| `EMPLOYER` | CSV, JSON `FEATURES` | `VENDOR_EMPLOYER_NAME` | `EMPLOYER` |
| `RECORD_TYPE` | CSV, JSON `FEATURES` | `VENDOR_PARTY_TYPE` | `RECORD_TYPE` |
| `REL_ANCHOR_KEY` | CSV, JSON `FEATURES` | `VENDOR_LINK_ID` | `REL_ANCHOR_KEY` |
| `REL_POINTER_KEY` | CSV, JSON `FEATURES` | `VENDOR_LINKED_TO_ID` | `REL_POINTER_KEY` |
| `REL_POINTER_ROLE` | CSV, JSON `FEATURES` | `VENDOR_LINK_ROLE` | `REL_POINTER_ROLE` |
| `CATEGORY` | CSV, JSON | `PARTY_ROLE` | none (payload, not used for matching) |
| `DATE` | CSV, JSON | `REGISTRATION_DATE` | none (payload, not used for matching) |
| `STATUS` | CSV, JSON | `LICENSE_STATUS` | none (payload, not used for matching) |
| `NATIONAL_ID_NUMBER` | CSV, JSON `FEATURES` | `VENDOR_NATIONAL_ID_NO` | `NATIONAL_ID_NUMBER` |
| `NATIONAL_ID_TYPE` | CSV, JSON `FEATURES` | `VENDOR_NATIONAL_ID_KIND` | `NATIONAL_ID_TYPE` |
| `NATIONAL_ID_COUNTRY` | CSV, JSON `FEATURES` | `VENDOR_NATIONAL_ID_ISSUER` | `NATIONAL_ID_COUNTRY` |
| `TAX_ID_NUMBER` | CSV, JSON `FEATURES` | `VENDOR_TAX_ID` | `TAX_ID_NUMBER` |
| `TAX_ID_TYPE` | CSV, JSON `FEATURES` | `VENDOR_TAX_ID_KIND` | `TAX_ID_TYPE` |
| `TAX_ID_COUNTRY` | CSV, JSON `FEATURES` | `VENDOR_TAX_ID_ISSUER` | `TAX_ID_COUNTRY` |
| `LEI_NUMBER` | CSV, JSON `FEATURES` | `VENDOR_LEI` | `LEI_NUMBER` |
| `NAME_ORG` | JSON `FEATURES` | `VENDOR_BUSINESS_NAME` | `NAME_ORG` |
| `REL_ANCHOR_DOMAIN` | JSON `FEATURES` | `VENDOR_LINK_DOMAIN` | `REL_ANCHOR_DOMAIN` |
| `NAME_FULL` | JSON `FEATURES` | `VENDOR_FULL_NAME` | `NAME_FULL` |
| `REL_POINTER_DOMAIN` | JSON `FEATURES` | `VENDOR_LINKED_TO_DOMAIN` | `REL_POINTER_DOMAIN` |
| `NAME_TYPE` | JSON `FEATURES` | `VENDOR_NAME_KIND` | `NAME_TYPE` |
| `NAME_LAST` | JSON `FEATURES` | `VENDOR_LAST_NAME` | `NAME_LAST` |
| `NAME_FIRST` | JSON `FEATURES` | `VENDOR_FIRST_NAME` | `NAME_FIRST` |

#### `SIU_WATCHLIST` (from `watchlist.csv` and `watchlist.jsonl`)

| Truth-set key | Found in | Story name | Senzing attribute on load |
| --- | --- | --- | --- |
| `PRIMARY_NAME_FULL` | CSV | `SIU_LISTED_FULL_NAME` | `PRIMARY_NAME_FULL` |
| `PRIMARY_NAME_LAST` | CSV | `SIU_LISTED_LAST_NAME` | `PRIMARY_NAME_LAST` |
| `PRIMARY_NAME_FIRST` | CSV | `SIU_LISTED_FIRST_NAME` | `PRIMARY_NAME_FIRST` |
| `PRIMARY_NAME_MIDDLE` | CSV | `SIU_LISTED_MIDDLE_NAME` | `PRIMARY_NAME_MIDDLE` |
| `NATIVE_NAME_FULL` | CSV | `SIU_LISTED_NAME_NATIVE_SCRIPT` | `NATIVE_NAME_FULL` |
| `GENDER` | CSV, JSON `FEATURES` | `SIU_LISTED_GENDER` | `GENDER` |
| `DATE_OF_BIRTH` | CSV, JSON `FEATURES` | `SIU_LISTED_BIRTH_DATE` | `DATE_OF_BIRTH` |
| `DRIVERS_LICENSE_NUMBER` | CSV, JSON `FEATURES` | `SIU_LISTED_DRIVER_LICENSE_NO` | `DRIVERS_LICENSE_NUMBER` |
| `DRIVERS_LICENSE_STATE` | CSV, JSON `FEATURES` | `SIU_LISTED_DRIVER_LICENSE_ISSUER` | `DRIVERS_LICENSE_STATE` |
| `SSN_NUMBER` | CSV, JSON `FEATURES` | `SIU_LISTED_SSN` | `SSN_NUMBER` |
| `EMAIL_ADDRESS` | CSV, JSON `FEATURES` | `SIU_LISTED_EMAIL` | `EMAIL_ADDRESS` |
| `ADDR_LINE1` | CSV, JSON `FEATURES` | `SIU_LISTED_STREET` | `ADDR_LINE1` |
| `ADDR_CITY` | CSV, JSON `FEATURES` | `SIU_LISTED_CITY` | `ADDR_CITY` |
| `ADDR_STATE` | CSV, JSON `FEATURES` | `SIU_LISTED_STATE` | `ADDR_STATE` |
| `ADDR_POSTAL_CODE` | CSV, JSON `FEATURES` | `SIU_LISTED_POSTAL_CODE` | `ADDR_POSTAL_CODE` |
| `ADDR_COUNTRY` | CSV, JSON `FEATURES` | `SIU_LISTED_COUNTRY` | `ADDR_COUNTRY` |
| `ADDR_TYPE` | CSV, JSON `FEATURES` | `SIU_LISTED_ADDRESS_KIND` | `ADDR_TYPE` |
| `PHONE_NUMBER` | CSV, JSON `FEATURES` | `SIU_LISTED_PHONE` | `PHONE_NUMBER` |
| `EMPLOYER` | CSV, JSON `FEATURES` | `SIU_LISTED_EMPLOYER_NAME` | `EMPLOYER` |
| `RECORD_TYPE` | CSV, JSON `FEATURES` | `SIU_LISTED_PARTY_TYPE` | `RECORD_TYPE` |
| `CATEGORY` | CSV, JSON | `LIST_TYPE` | none (payload, not used for matching) |
| `DATE` | CSV, JSON | `DATE_LISTED` | none (payload, not used for matching) |
| `STATUS` | CSV, JSON | `LISTING_STATUS` | none (payload, not used for matching) |
| `NAME_LAST` | JSON `FEATURES` | `SIU_LISTED_LAST_NAME` | `NAME_LAST` |
| `NAME_FIRST` | JSON `FEATURES` | `SIU_LISTED_FIRST_NAME` | `NAME_FIRST` |
| `NAME_MIDDLE` | JSON `FEATURES` | `SIU_LISTED_MIDDLE_NAME` | `NAME_MIDDLE` |
| `NAME_FULL` | JSON `FEATURES` | `SIU_LISTED_FULL_NAME` | `NAME_FULL` |

### On-screen value labels

These change only how a value is shown, never the stored value.

- `POLICY_STATUS`: show "Terminated" as "Cancelled".
- `LIST_TYPE`: show "Fraud" as "Known claims fraud".

## Notes for reviewers

- One generic email address (`info@ca-state.gov`) appears on records for 19 different entities. Treat it as a shared contact address, not a ring, and check that the domain doesn't belong to a real organization before showing it.
- One address is shared by 5 entities and one email by 4. These may make good "ring or household?" examples, depending on what Senzing reports on load.
- The vendor registry includes a co-owner named Howard Hughes, which is also the name of a well-known real person. Don't feature that record on stage.
