# senzing-bootcamp-business-cases

Versioned business-case briefs for [Senzing Bootcamp] demonstrations in Claude, ChatGPT, Kiro and Copilot.

A brief turns the Senzing demo truth set (`Senzing/truth-sets`, `truthsets/demo`) into an industry story.
Each brief has a scenario, the steps to take after approval, label mappings and notes for reviewers.
All records in the truth set are synthetic.

## Business cases

| Business case | Brief | Status |
| --- | --- | --- |
| Banking KYC/KYB and AML | [demo-truthset-banking-kyc-kyb-aml.md](business-cases/banking-kyc-kyb-aml/demo-truthset-banking-kyc-kyb-aml.md) | Draft |
| P&C insurance and claims fraud | [demo-truthset-pc-insurance-claims-fraud.md](business-cases/pc-insurance-claims-fraud/demo-truthset-pc-insurance-claims-fraud.md) | Draft |

> **Note:** A brief marked "Status: draft for human review" is not ready for a live demo.
> Use it only after a reviewer approves it and its status line says so.

## Using a brief

1. Pick a release tag from the [releases] or tags list.
   Pin to a tag rather than `main`, so the brief doesn't change under you.
2. Get the brief from that tag, in either of these ways:
   - download the file, or
   - copy its raw link: `https://raw.githubusercontent.com/docktermj/senzing-bootcamp-business-cases/<tag>/business-cases/<case>/demo-truthset-<case>.md`
3. Give the brief to the assistant (Claude, ChatGPT, Kiro or Copilot) by uploading the file or pasting the link or its text.
   Briefs are assistant-neutral and work unchanged in all four.

## Adding a business case

1. Choose a short, lowercase, hyphenated case name, for example `healthcare-patient-matching`.
2. Create the folder `business-cases/<case>/` and put the brief in it as `demo-truthset-<case>.md`.
3. Start the brief with the same header as the existing ones: a title, the "Status" line and the "Prepared" date.
4. Add a row to the [Business cases](#business-cases) table above.
5. Add an entry under `[Unreleased]` in [CHANGELOG.md](CHANGELOG.md).

A variant for one assistant goes in the same folder with a suffix, for example `demo-truthset-<case>-kiro.md`.
Add one only when that assistant needs different wording.

## Versioning

The repository as a whole is versioned with [Semantic Versioning] tags, and every release is recorded in [CHANGELOG.md](CHANGELOG.md).
Because people get briefs from a tag by download or raw link, **a brief's path is a public contract**.

- **MAJOR:** a change that breaks a demo already in use, such as a renamed or moved brief, renamed data sources, or a removed business case.
- **MINOR:** a new business case or a new assistant-specific variant.
- **PATCH:** wording, label or reviewer-note fixes.

Each brief keeps its status line (draft or approved) and its "Prepared" date in its header.

## Linting

Every pull request and push to `main` runs markdownlint against [.markdownlint-cli2.yaml](.markdownlint-cli2.yaml).
Run the same check locally from the repository root:

```console
npx markdownlint-cli2 "**/*.md"
```

[releases]: https://github.com/docktermj/senzing-bootcamp-business-cases/releases
[Semantic Versioning]: https://semver.org/
[Senzing Bootcamp]: https://github.com/senzing/bootcamp
