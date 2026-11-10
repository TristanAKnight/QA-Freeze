# QA Freeze

**Approve it once. Know when it changes.**

QA Freeze is an offline-first verification and approval workflow for individual PNG assets. It is designed for small game and digital-art teams that need to know whether the exact file approved by an artist is the exact file later handed to a developer.

> Current status: **design/RFC stage**. This repository does not claim working software, validated filesystem semantics, benchmark results, or production readiness yet.

QA Freeze grew out of a repeated game-art QA problem: visual approval, file integrity, baseline health, technical compliance, and handoff status are separate questions. Treating them as one generic PASS/FAIL result makes mistakes harder to diagnose.

## What QA Freeze is for

QA Freeze aims to:

- preserve the exact approved bytes without re-encoding the source PNG;
- attach an explicit human approval record to that baseline;
- detect changed, missing, corrupt, unapproved, or policy-noncompliant assets;
- keep approval state separate from byte-integrity state;
- provide deterministic machine-readable results and concise human-readable summaries;
- work locally without uploading proprietary artwork or credentials.

## What QA Freeze does not claim

QA Freeze does not:

- make files uneditable;
- provide tamper-proof security against a malicious local administrator;
- reproduce or control generative-image models;
- enforce aesthetic consistency;
- replace ordinary backups or version control;
- claim broad market demand before user validation;
- claim public-release readiness before the defined engineering gates pass.

The word **freeze** means an auditable approved baseline plus later verification. It does not mean the operating system or filesystem becomes immutable.

## Public-safety boundary

This repository is intentionally separated from private game-production work.

Do not commit:

- proprietary game assets;
- private UI designs;
- client files;
- internal prompts;
- credentials or tokens;
- copied private repository history;
- personal documents;
- anything that requires a private account or connector to function.

Tests and examples must use synthetic or independently redistributable material.

## Project stage

The immediate target is a bounded Python CLI proof of concept.

Planned v0.1 workflow:

```text
qa-freeze init ./my-game
cd ./my-game
qa-freeze scan --json scan.json
qa-freeze approve game-assets/ui/start_button.png --note "Approved by art lead"
qa-freeze verify --json verification.json
```

The CLI names above are design targets, not proof that the commands already exist.

## Engineering principles

1. **Exact bytes first.** SHA-256 verifies the approved file identity.
2. **Approval is explicit.** A hash alone is not human approval.
3. **State is multidimensional.** Approval, source health, baseline integrity, and technical policy are reported independently.
4. **Source files are read-only by default.** Scan and verify never rewrite source assets.
5. **Publishing approval must be crash-safe.** Interrupted approval cannot silently replace the previous authoritative state.
6. **Evidence is retained.** Recovery should prefer traceability over automatic deletion.
7. **Provider/network independence.** Core operation requires no network access or telemetry.
8. **Claims follow tests.** Cross-platform behavior, locking, performance, and adoption must be measured rather than assumed.

## Repository map

```text
.
├── README.md
├── SPECIFICATION.md
├── AUTHORS.md
├── SECURITY.md
├── CONTRIBUTING.md
├── CHANGELOG.md
├── ROADMAP.md
├── docs/
│   ├── adr/
│   │   └── 0001-exact-file-integrity.md
│   ├── threat-model.md
│   └── release-gates.md
├── schemas/
├── src/
├── tests/
├── fixtures/
│   └── synthetic/
└── examples/
    └── sample-reports/
```

## Release philosophy

A public repository is not the same as a public software release.

Publishing the specification is allowed after IP/name review. Executable releases, package publication, or production-readiness claims require the relevant release gates, tests, ownership review, dependency/license review, and security review.

See [SPECIFICATION.md](SPECIFICATION.md) and [docs/release-gates.md](docs/release-gates.md).

## Authors

- **Tristan Knight** — project and engineering lead, primary maintainer.
- **Katerina** — product workflow, QA methodology, creative review, project assistant.

See [AUTHORS.md](AUTHORS.md).

## License

Original code and documentation in this repository are released under the [MIT License](LICENSE), subject to the separate rights of any future sample artwork or third-party dependencies.

Synthetic fixture artwork must state its own redistribution status when added.
