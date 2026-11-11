# Contributing

Corrections, technical clarifications, and firsthand observations are welcome. This repository documents the owner's real troubleshooting cases. A community submission is a suggestion until reviewed; it is not evidence that the owner personally reproduced or verified it.

## Suggest a correction or solution

Open an [issue](https://github.com/selm4n/field-fixes/issues/new/choose) using the troubleshooting template. For an existing entry, link to the article and explain the correction, supporting evidence, and relevant environment differences. Small wording or link corrections can be submitted directly as a pull request.

Clearly distinguish:

- **Tested by you:** what you did, on which hardware/software versions, and how you verified the result.
- **Confirmed by the owner:** only when the owner has explicitly confirmed that result.
- **Hypothesis or untested suggestion:** a possible explanation or next step, not a proven fix.

Do not submit unsupported claims, invented test results, or copied troubleshooting articles. Write explanations in your own words and link to relevant primary documentation. Do not assume that an error message establishes its underlying cause.

## Add a documented case

1. Copy [templates/fix-template.md](templates/fix-template.md) to a descriptive, lowercase, hyphenated filename in the appropriate category, such as `windows/bluetooth/device-error-code.md`.
2. Keep one problem per file. Add categories only as needed, avoid duplicate articles, and keep nesting shallow.
3. Fill in the template. Use `Not recorded` for unavailable facts. Record dates as `YYYY-MM-DD`; use `Not applicable — unresolved` for a resolution date when appropriate.
4. Include exact errors, safe and reproducible steps, unsuccessful attempts, verification, limitations, and verified source links. Identify who tested the procedure and which steps or outcomes remain unverified.
5. Add the entry to the README Solutions table and update its category overview if needed. Community cases must be visibly attributed and must not be labeled owner-verified without confirmation.
6. Review Markdown, relative links, spelling, factual claims, and the full diff before submitting a pull request. Update the last-reviewed date when reviewing an entry; do not change its original resolution date.

## Protect private information

Issues, pull requests, and repository history are public. Never include credentials, access tokens, private email addresses, personal IP addresses, private network identifiers, identifying computer names, or customer/business information. Replace sensitive values with clear placeholders.

Only attach screenshots if sanitized copies have been explicitly reviewed. Check device-property screenshots for identifying instance paths and addresses. Do not upload raw logs or screenshots as a substitute for a minimal, redacted error excerpt. Ignore rules are only a convenience; review every staged file.

## Review and licensing

The maintainer may request evidence, narrow a claim, or leave a suggestion unverified. Prefer targeted changes over automated rewrites and unnecessary tooling. By submitting original content, you agree that it may be distributed under this repository's [MIT License](LICENSE); do not submit material you lack permission to license.
