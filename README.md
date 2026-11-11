# field-fixes

**Real problems. Tested solutions. Notes from the field.**

A public collection of technical problems encountered, investigated, and resolved firsthand by [selm4n](https://github.com/selm4n). Each entry preserves the symptoms, investigation, practical resolution, and limits of what was verified.

These are troubleshooting records, not universal recipes. Confirmed observations, possible explanations, and untested follow-up steps are identified separately. Community suggestions are attributed and are not presented as personally verified by the owner.

## Solutions

| Category | Problem | Status | Resolved |
| --- | --- | --- | --- |
| Windows / Bluetooth | [8BitDo Pro 2 Bluetooth driver error on Windows 11 — Code 10](windows/bluetooth/8bitdo-pro-2-code-10.md) | Resolved | 2026-11-10 |

## Categories

- **[Windows / Bluetooth](windows/bluetooth/)** — controller pairing and input-device troubleshooting.

New categories are added when there is a real case to document. Use the operating system as the primary category for platform-specific problems; reserve categories such as hardware, software, networking, and homelab for cases whose main scope fits there. Keep one canonical article per problem and link to it rather than duplicating it across categories. Search this repository for a device name, exact error message, or error code.

## How entries are documented

The [fix template](templates/fix-template.md) records environment details, symptoms and exact errors, diagnosis, unsuccessful attempts, the working procedure, verification, possible explanations, warnings, references, and review dates. Missing details are marked **Not recorded**.

A resolved case means the owner confirmed the outcome in the documented environment. It does not mean every suggested step was independently verified or that the root cause was proven. Hardware, operating-system builds, firmware, and application versions can affect results; read the limitations before making changes.

## Corrections and new cases

Technical corrections and additional observations are welcome through [Issues](https://github.com/selm4n/field-fixes/issues) or pull requests. See [CONTRIBUTING.md](CONTRIBUTING.md) for evidence, attribution, privacy, and submission guidance.

To add an entry, copy the template into the appropriate category, write down what was actually tested, and add a link to the Solutions table above. Create folders only when they contain a case.

## License

Original repository content is available under the [MIT License](LICENSE). Linked vendor documentation remains subject to its own terms. References provide context; they do not imply vendor endorsement of these fixes.
