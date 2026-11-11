<!-- markdownlint-disable-file MD024 -->
# Changelog

All notable changes to this project will be documented in this file.

The changelog format is based on [Keep a Changelog] and [CommonMark].
This project adheres to [Semantic Versioning].

## [Unreleased]

### Changed in Unreleased

- Both briefs link to the Senzing demo truth set at <https://github.com/Senzing/truth-sets/tree/main/truthsets/demo>
- Both briefs rename the truth set's metadata to fit the story: data-source names, CSV column names and JSON keys, including matching attributes, each with its Senzing attribute for loading
- Both briefs state that data values never change

## [0.1.0] - 2026-11-11

### Added to 0.1.0

- `CHANGELOG.md`
- `business-cases/<case>/` layout, one folder per business case
- Banking KYC/KYB and AML brief (draft)
- P&C insurance and claims fraud brief (draft)
- `README.md` with business cases, usage, how to add a case and the versioning policy
- Markdown linting in CI with `markdownlint-cli2`, configured in `.markdownlint-cli2.yaml`

[CommonMark]: https://commonmark.org/
[Keep a Changelog]: https://keepachangelog.com/
[Semantic Versioning]: https://semver.org/
