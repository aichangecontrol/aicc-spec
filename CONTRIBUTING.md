# Contributing

Thanks for helping improve the AI Change Control specification.

## Proposing changes

1. Open an issue describing the problem and desired change.
2. For non-trivial changes, propose a draft in a pull request.
3. Keep proposals spec-focused and implementation-agnostic.

## RFCs and breaking changes

Breaking changes require an RFC. Use the template in `rfcs/0001-rfc-template.md`.
Breaking changes must be accompanied by a migration guide.

## Code of conduct

This project follows the Contributor Covenant. See `CODE_OF_CONDUCT.md`.

## Validation

Use the AICC CLI to validate examples and proposed specs:

```bash
cd ../aicc-cli
npm install
npm test
```
