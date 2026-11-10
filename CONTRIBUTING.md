# Contributing to QA Freeze

QA Freeze is currently in design/prototype stage.

## Before contributing

Read:

1. [SPECIFICATION.md](SPECIFICATION.md)
2. [docs/threat-model.md](docs/threat-model.md)
3. [docs/release-gates.md](docs/release-gates.md)
4. [docs/adr/0001-exact-file-integrity.md](docs/adr/0001-exact-file-integrity.md)

## Public-safe contributions only

Do not submit:

- proprietary game art;
- private client files;
- credentials;
- personal documents;
- private prompts;
- copied private repository code/history;
- examples requiring private services/accounts.

Use synthetic fixtures.

## Engineering expectations

Core changes should include tests for behavior and failure modes.

Integrity/storage work should prioritize:

- deterministic results;
- fault injection;
- crash/recovery behavior;
- path containment;
- concurrency;
- no unintended source mutation.

A coverage percentage does not substitute for proving the failure semantics.

## Scope discipline

v0.1 intentionally defers visual diff, GUI, HTML reports, restore, semantic image comparison, and automatic rename detection.

A contribution that pulls deferred functionality into v0.1 must explain why the reliability cost is justified.

## Pull requests

PRs should state:

- problem;
- approach;
- relevant acceptance criteria;
- tests added/changed;
- security/privacy impact;
- compatibility/schema impact;
- whether the change alters a documented release gate.

## Commit hygiene

Keep commits reviewable and free of private project material.

Do not squash unrelated refactors into integrity/security changes.

## Licensing

By contributing original code or documentation, you represent that you have the right to submit it under the repository's MIT license.

Artwork and other media may have separate licensing and must carry explicit redistribution terms.
