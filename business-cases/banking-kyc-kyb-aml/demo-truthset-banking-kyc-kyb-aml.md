# Demo data brief: Banking KYC/KYB + AML (Senzing demo truth set)

> **Status: draft for human review. Do not load until a reviewer approves it.**
> **Source data:** every record comes from the Senzing demo truth set at <https://github.com/Senzing/truth-sets/tree/main/truthsets/demo>. All records are synthetic, and none of them are real people or companies.
> This brief renames the truth set's metadata (data-source names, CSV column names and JSON keys) to fit the story. It never changes a data value.
> Prepared Nov 9, 2026.

## Scenario

A fictional US bank wants one trusted view of every customer. That way the people and AI agents who serve customers know who they're dealing with and who each customer is connected to, and the bank can meet its KYC, KYB and AML duties. The demo tells the story in three parts. The examples come from the truth set's answer key and should be confirmed on load.

**1. Customer 360 (KYC): one view of each customer.** The customer records (`BANK_CUSTOMERS`, 120 records) hold the same people and businesses many times over, under nicknames, misspellings, other alphabets, old addresses and partial IDs. Senzing resolves them into single customers, each with every account in one place. For example, six records for Susan Mooney, spelled five different ways, become one customer. Senzing also keeps apart people who only look alike.

**2. Who they're connected to (KYB).** A business registry (`BUSINESS_REGISTRY`, 22 records) adds each business's owners (40% to 100%), directors, principals and parent companies, and links them to the resolved customers. For example, the Platinum business customer "Mullenkrants" is the registered partnership Müllenkranz, which is 100% owned by Aleksandr Vasiliev and has Faisal Siddiqui as president. The business customers "Univrsl Export Inc", "Singapore exports" and "India Exports" turn out to belong to one corporate family, Universal Exports.

**3. AML screening.** The resolved customers and their owners are screened against a watchlist (`AML_WATCHLIST`, 17 records) of sanctioned people, politically exposed persons (PEPs) and people the bank has flagged for fraud. This catches matches a name-only check misses. For example, Vasiliev is also a Platinum customer under the spelling "Alexander Pavlovich Vasiliev", and he is on the sanctions list in Cyrillic script; Siddiqui's sanctions listing is in Arabic script. Margaret Charney, a 50% owner of Universal Exports Worldwide, is a PEP listed as "Peggie Charney". Edward Kusha, whose account was once closed under "Ed Knight", is on the internal fraud list under both names. Robert Smith, a different customer, is kept apart from Robert Smith Sr on that list. Each match is shown as a lead with evidence, not a verdict.

## What to do after approval

1. Download the source files from the [truth set](https://github.com/Senzing/truth-sets/tree/main/truthsets/demo): `customers`, `reference` and `watchlist` (as `.csv` or `.jsonl`), plus `actual_truthset_key.csv`, `alternate_truthset_key.csv` and `truthset_config.g2c`.
2. Rename the three data sources as shown in [Data sources](#data-sources), in the record files, in both answer keys and in `truthset_config.g2c`.
3. Rename every field as shown in [Field names](#field-names). Change no data value.
4. Map each story name back to the Senzing attribute in its table, then load the three files into a fresh Senzing instance through the Senzing MCP server.
5. Compare the results with the renamed `actual_truthset_key.csv` and report any differences. Present relationships and watchlist hits as results found on load, not as expected facts.

## Metadata mapping

### Data sources

| Existing | New | Role in the story |
|---|---|---|
| `CUSTOMERS` | `BANK_CUSTOMERS` | The bank's customer and account records, for people and businesses |
| `REFERENCE` | `BUSINESS_REGISTRY` | Third-party company and beneficial-ownership data used for KYB |
| `WATCHLIST` | `AML_WATCHLIST` | Sanctions, PEP and internal fraud lists used for screening |

Apply these names everywhere the old ones appear: the `DATA_SOURCE` value of every record, the `DATA_SOURCE` column of `actual_truthset_key.csv` and `alternate_truthset_key.csv`, and the `addDataSource` lines in `truthset_config.g2c`.

### Rules for every rename

- **Rename metadata only.** Apply the data-source names and the field names below. Keep every data value exactly as it is: names, dates, addresses, identifiers, phone numbers, emails, relationship keys, record IDs and every other value. The truth set is tuned so that each value illustrates an entity-resolution principle, and `actual_truthset_key.csv` depends on those values.
- **Keep `DATA_SOURCE` and `RECORD_ID` as key names.** Only the data-source *values* change.
- **Keep the JSON structure.** In the JSONL files, rename keys in place, both the top-level keys and the keys inside each record's `FEATURES` array.
- **Map back exactly.** Before loading, map each story name back to the Senzing attribute in its table's last column. Don't infer a mapping from the story names.
- Fields marked "payload, not used for matching" load as ordinary record fields under their story names.

### Field names

Each table lists every key in that data source's CSV and JSONL files, except `DATA_SOURCE` and `RECORD_ID`. "Found in" says which files have the key: `CSV` for the CSV header, `JSON` for a top-level JSONL key, and `JSON FEATURES` for a key inside `FEATURES`.

#### `BANK_CUSTOMERS` (from `customers.csv` and `customers.jsonl`)

| Truth-set key | Found in | Story name | Senzing attribute on load |
| --- | --- | --- | --- |
| `PRIMARY_NAME_FULL` | CSV | `CUSTOMER_FULL_NAME` | `PRIMARY_NAME_FULL` |
| `PRIMARY_NAME_ORG` | CSV | `CUSTOMER_BUSINESS_NAME` | `PRIMARY_NAME_ORG` |
| `PRIMARY_NAME_LAST` | CSV | `CUSTOMER_LAST_NAME` | `PRIMARY_NAME_LAST` |
| `PRIMARY_NAME_FIRST` | CSV | `CUSTOMER_FIRST_NAME` | `PRIMARY_NAME_FIRST` |
| `PRIMARY_NAME_MIDDLE` | CSV | `CUSTOMER_MIDDLE_NAME` | `PRIMARY_NAME_MIDDLE` |
| `NATIVE_NAME_FULL` | CSV | `CUSTOMER_NAME_NATIVE_SCRIPT` | `NATIVE_NAME_FULL` |
| `SECONDARY_NAME_ORG` | CSV | `CUSTOMER_BUSINESS_NAME_ALT` | `SECONDARY_NAME_ORG` |
| `GENDER` | CSV, JSON `FEATURES` | `CUSTOMER_GENDER` | `GENDER` |
| `DATE_OF_BIRTH` | CSV, JSON `FEATURES` | `CUSTOMER_BIRTH_DATE` | `DATE_OF_BIRTH` |
| `PASSPORT_NUMBER` | CSV, JSON `FEATURES` | `CUSTOMER_PASSPORT_NO` | `PASSPORT_NUMBER` |
| `PASSPORT_COUNTRY` | CSV, JSON `FEATURES` | `CUSTOMER_PASSPORT_ISSUER` | `PASSPORT_COUNTRY` |
| `DRIVERS_LICENSE_NUMBER` | CSV, JSON `FEATURES` | `CUSTOMER_DRIVER_LICENSE_NO` | `DRIVERS_LICENSE_NUMBER` |
| `DRIVERS_LICENSE_STATE` | CSV, JSON `FEATURES` | `CUSTOMER_DRIVER_LICENSE_ISSUER` | `DRIVERS_LICENSE_STATE` |
| `SSN_NUMBER` | CSV, JSON `FEATURES` | `CUSTOMER_SSN` | `SSN_NUMBER` |
| `NATIONAL_ID_NUMBER` | CSV, JSON `FEATURES` | `CUSTOMER_NATIONAL_ID_NO` | `NATIONAL_ID_NUMBER` |
| `NATIONAL_ID_COUNTRY` | CSV, JSON `FEATURES` | `CUSTOMER_NATIONAL_ID_ISSUER` | `NATIONAL_ID_COUNTRY` |
| `EMAIL_ADDRESS` | CSV, JSON `FEATURES` | `CUSTOMER_EMAIL` | `EMAIL_ADDRESS` |
| `ADDR_FULL` | CSV, JSON `FEATURES` | `CUSTOMER_ADDRESS` | `ADDR_FULL` |
| `ADDR_LINE1` | CSV, JSON `FEATURES` | `CUSTOMER_STREET` | `ADDR_LINE1` |
| `ADDR_CITY` | CSV, JSON `FEATURES` | `CUSTOMER_CITY` | `ADDR_CITY` |
| `ADDR_STATE` | CSV, JSON `FEATURES` | `CUSTOMER_STATE` | `ADDR_STATE` |
| `ADDR_POSTAL_CODE` | CSV, JSON `FEATURES` | `CUSTOMER_POSTAL_CODE` | `ADDR_POSTAL_CODE` |
| `ADDR_COUNTRY` | CSV, JSON `FEATURES` | `CUSTOMER_COUNTRY` | `ADDR_COUNTRY` |
| `ADDR_TYPE` | CSV, JSON `FEATURES` | `CUSTOMER_ADDRESS_KIND` | `ADDR_TYPE` |
| `PHONE_NUMBER` | CSV, JSON `FEATURES` | `CUSTOMER_PHONE` | `PHONE_NUMBER` |
| `PHONE_TYPE` | CSV, JSON `FEATURES` | `CUSTOMER_PHONE_KIND` | `PHONE_TYPE` |
| `RECORD_TYPE` | CSV, JSON `FEATURES` | `CUSTOMER_PARTY_TYPE` | `RECORD_TYPE` |
| `AMOUNT` | CSV, JSON | `ACCOUNT_BALANCE` | none (payload, not used for matching) |
| `CATEGORY` | CSV, JSON | `CUSTOMER_TIER` | none (payload, not used for matching) |
| `DATE` | CSV, JSON | `ACCOUNT_OPEN_DATE` | none (payload, not used for matching) |
| `STATUS` | CSV, JSON | `ACCOUNT_STATUS` | none (payload, not used for matching) |
| `NAME_LAST` | JSON `FEATURES` | `CUSTOMER_LAST_NAME` | `NAME_LAST` |
| `NAME_FIRST` | JSON `FEATURES` | `CUSTOMER_FIRST_NAME` | `NAME_FIRST` |
| `NAME_MIDDLE` | JSON `FEATURES` | `CUSTOMER_MIDDLE_NAME` | `NAME_MIDDLE` |
| `NAME_FULL` | JSON `FEATURES` | `CUSTOMER_FULL_NAME` | `NAME_FULL` |
| `NAME_ORG` | JSON `FEATURES` | `CUSTOMER_BUSINESS_NAME` | `NAME_ORG` |
| `NAME_TYPE` | JSON `FEATURES` | `CUSTOMER_NAME_KIND` | `NAME_TYPE` |

#### `BUSINESS_REGISTRY` (from `reference.csv` and `reference.jsonl`)

| Truth-set key | Found in | Story name | Senzing attribute on load |
| --- | --- | --- | --- |
| `PRIMARY_NAME_FULL` | CSV | `REGISTRY_FULL_NAME` | `PRIMARY_NAME_FULL` |
| `PRIMARY_NAME_ORG` | CSV | `REGISTRY_BUSINESS_NAME` | `PRIMARY_NAME_ORG` |
| `PRIMARY_NAME_LAST` | CSV | `REGISTRY_LAST_NAME` | `PRIMARY_NAME_LAST` |
| `PRIMARY_NAME_FIRST` | CSV | `REGISTRY_FIRST_NAME` | `PRIMARY_NAME_FIRST` |
| `NATIVE_NAME_FULL` | CSV | `REGISTRY_NAME_NATIVE_SCRIPT` | `NATIVE_NAME_FULL` |
| `SECONDARY_NAME_ORG` | CSV | `REGISTRY_BUSINESS_NAME_ALT` | `SECONDARY_NAME_ORG` |
| `DATE_OF_BIRTH` | CSV, JSON `FEATURES` | `REGISTRY_BIRTH_DATE` | `DATE_OF_BIRTH` |
| `EMAIL_ADDRESS` | CSV, JSON `FEATURES` | `REGISTRY_EMAIL` | `EMAIL_ADDRESS` |
| `ADDR_FULL` | CSV, JSON `FEATURES` | `REGISTRY_ADDRESS` | `ADDR_FULL` |
| `ADDR_LINE1` | CSV, JSON `FEATURES` | `REGISTRY_STREET` | `ADDR_LINE1` |
| `ADDR_CITY` | CSV, JSON `FEATURES` | `REGISTRY_CITY` | `ADDR_CITY` |
| `ADDR_STATE` | CSV, JSON `FEATURES` | `REGISTRY_STATE` | `ADDR_STATE` |
| `ADDR_POSTAL_CODE` | CSV, JSON `FEATURES` | `REGISTRY_POSTAL_CODE` | `ADDR_POSTAL_CODE` |
| `ADDR_COUNTRY` | CSV, JSON `FEATURES` | `REGISTRY_COUNTRY` | `ADDR_COUNTRY` |
| `ADDR_TYPE` | CSV, JSON `FEATURES` | `REGISTRY_ADDRESS_KIND` | `ADDR_TYPE` |
| `PHONE_NUMBER` | CSV, JSON `FEATURES` | `REGISTRY_PHONE` | `PHONE_NUMBER` |
| `PHONE_TYPE` | CSV, JSON `FEATURES` | `REGISTRY_PHONE_KIND` | `PHONE_TYPE` |
| `EMPLOYER` | CSV, JSON `FEATURES` | `REGISTRY_EMPLOYER_NAME` | `EMPLOYER` |
| `RECORD_TYPE` | CSV, JSON `FEATURES` | `REGISTRY_PARTY_TYPE` | `RECORD_TYPE` |
| `REL_ANCHOR_KEY` | CSV, JSON `FEATURES` | `REGISTRY_LINK_ID` | `REL_ANCHOR_KEY` |
| `REL_POINTER_KEY` | CSV, JSON `FEATURES` | `REGISTRY_LINKED_TO_ID` | `REL_POINTER_KEY` |
| `REL_POINTER_ROLE` | CSV, JSON `FEATURES` | `REGISTRY_LINK_ROLE` | `REL_POINTER_ROLE` |
| `CATEGORY` | CSV, JSON | `PARTY_ROLE` | none (payload, not used for matching) |
| `DATE` | CSV, JSON | `REGISTRATION_DATE` | none (payload, not used for matching) |
| `STATUS` | CSV, JSON | `REGISTRATION_STATUS` | none (payload, not used for matching) |
| `NATIONAL_ID_NUMBER` | CSV, JSON `FEATURES` | `REGISTRY_NATIONAL_ID_NO` | `NATIONAL_ID_NUMBER` |
| `NATIONAL_ID_TYPE` | CSV, JSON `FEATURES` | `REGISTRY_NATIONAL_ID_KIND` | `NATIONAL_ID_TYPE` |
| `NATIONAL_ID_COUNTRY` | CSV, JSON `FEATURES` | `REGISTRY_NATIONAL_ID_ISSUER` | `NATIONAL_ID_COUNTRY` |
| `TAX_ID_NUMBER` | CSV, JSON `FEATURES` | `REGISTRY_TAX_ID` | `TAX_ID_NUMBER` |
| `TAX_ID_TYPE` | CSV, JSON `FEATURES` | `REGISTRY_TAX_ID_KIND` | `TAX_ID_TYPE` |
| `TAX_ID_COUNTRY` | CSV, JSON `FEATURES` | `REGISTRY_TAX_ID_ISSUER` | `TAX_ID_COUNTRY` |
| `LEI_NUMBER` | CSV, JSON `FEATURES` | `REGISTRY_LEI` | `LEI_NUMBER` |
| `NAME_ORG` | JSON `FEATURES` | `REGISTRY_BUSINESS_NAME` | `NAME_ORG` |
| `REL_ANCHOR_DOMAIN` | JSON `FEATURES` | `REGISTRY_LINK_DOMAIN` | `REL_ANCHOR_DOMAIN` |
| `NAME_FULL` | JSON `FEATURES` | `REGISTRY_FULL_NAME` | `NAME_FULL` |
| `REL_POINTER_DOMAIN` | JSON `FEATURES` | `REGISTRY_LINKED_TO_DOMAIN` | `REL_POINTER_DOMAIN` |
| `NAME_TYPE` | JSON `FEATURES` | `REGISTRY_NAME_KIND` | `NAME_TYPE` |
| `NAME_LAST` | JSON `FEATURES` | `REGISTRY_LAST_NAME` | `NAME_LAST` |
| `NAME_FIRST` | JSON `FEATURES` | `REGISTRY_FIRST_NAME` | `NAME_FIRST` |

#### `AML_WATCHLIST` (from `watchlist.csv` and `watchlist.jsonl`)

| Truth-set key | Found in | Story name | Senzing attribute on load |
| --- | --- | --- | --- |
| `PRIMARY_NAME_FULL` | CSV | `AML_LISTED_FULL_NAME` | `PRIMARY_NAME_FULL` |
| `PRIMARY_NAME_LAST` | CSV | `AML_LISTED_LAST_NAME` | `PRIMARY_NAME_LAST` |
| `PRIMARY_NAME_FIRST` | CSV | `AML_LISTED_FIRST_NAME` | `PRIMARY_NAME_FIRST` |
| `PRIMARY_NAME_MIDDLE` | CSV | `AML_LISTED_MIDDLE_NAME` | `PRIMARY_NAME_MIDDLE` |
| `NATIVE_NAME_FULL` | CSV | `AML_LISTED_NAME_NATIVE_SCRIPT` | `NATIVE_NAME_FULL` |
| `GENDER` | CSV, JSON `FEATURES` | `AML_LISTED_GENDER` | `GENDER` |
| `DATE_OF_BIRTH` | CSV, JSON `FEATURES` | `AML_LISTED_BIRTH_DATE` | `DATE_OF_BIRTH` |
| `DRIVERS_LICENSE_NUMBER` | CSV, JSON `FEATURES` | `AML_LISTED_DRIVER_LICENSE_NO` | `DRIVERS_LICENSE_NUMBER` |
| `DRIVERS_LICENSE_STATE` | CSV, JSON `FEATURES` | `AML_LISTED_DRIVER_LICENSE_ISSUER` | `DRIVERS_LICENSE_STATE` |
| `SSN_NUMBER` | CSV, JSON `FEATURES` | `AML_LISTED_SSN` | `SSN_NUMBER` |
| `EMAIL_ADDRESS` | CSV, JSON `FEATURES` | `AML_LISTED_EMAIL` | `EMAIL_ADDRESS` |
| `ADDR_LINE1` | CSV, JSON `FEATURES` | `AML_LISTED_STREET` | `ADDR_LINE1` |
| `ADDR_CITY` | CSV, JSON `FEATURES` | `AML_LISTED_CITY` | `ADDR_CITY` |
| `ADDR_STATE` | CSV, JSON `FEATURES` | `AML_LISTED_STATE` | `ADDR_STATE` |
| `ADDR_POSTAL_CODE` | CSV, JSON `FEATURES` | `AML_LISTED_POSTAL_CODE` | `ADDR_POSTAL_CODE` |
| `ADDR_COUNTRY` | CSV, JSON `FEATURES` | `AML_LISTED_COUNTRY` | `ADDR_COUNTRY` |
| `ADDR_TYPE` | CSV, JSON `FEATURES` | `AML_LISTED_ADDRESS_KIND` | `ADDR_TYPE` |
| `PHONE_NUMBER` | CSV, JSON `FEATURES` | `AML_LISTED_PHONE` | `PHONE_NUMBER` |
| `EMPLOYER` | CSV, JSON `FEATURES` | `AML_LISTED_EMPLOYER_NAME` | `EMPLOYER` |
| `RECORD_TYPE` | CSV, JSON `FEATURES` | `AML_LISTED_PARTY_TYPE` | `RECORD_TYPE` |
| `CATEGORY` | CSV, JSON | `LIST_TYPE` | none (payload, not used for matching) |
| `DATE` | CSV, JSON | `DATE_LISTED` | none (payload, not used for matching) |
| `STATUS` | CSV, JSON | `LISTING_STATUS` | none (payload, not used for matching) |
| `NAME_LAST` | JSON `FEATURES` | `AML_LISTED_LAST_NAME` | `NAME_LAST` |
| `NAME_FIRST` | JSON `FEATURES` | `AML_LISTED_FIRST_NAME` | `NAME_FIRST` |
| `NAME_MIDDLE` | JSON `FEATURES` | `AML_LISTED_MIDDLE_NAME` | `NAME_MIDDLE` |
| `NAME_FULL` | JSON `FEATURES` | `AML_LISTED_FULL_NAME` | `NAME_FULL` |

### On-screen value labels

These change only how a value is shown, never the stored value.

- `ACCOUNT_STATUS`: show "Terminated" as "Closed by bank".
- `LIST_TYPE`: show "Fraud" as "Internal fraud list".

## Notes for reviewers

- One generic email address (`info@ca-state.gov`) appears on records for 19 different entities. Treat it as a shared contact address, not a link between them, and check that the domain doesn't belong to a real organization before showing it.
- One address is shared by 5 entities and one email by 4. These may make good "connected or not?" examples, depending on what Senzing reports on load.
- The business registry includes a co-owner named Howard Hughes, which is also the name of a well-known real person. Don't feature that record on stage; use Margaret Charney for the PEP example.
