# QA Freeze — Engineering Specification

**Document status:** Design/RFC  
**Revision:** 0.1-public  
**Former internal codename:** AssetLock  
**Date:** November 2026

This document converts the original QA Freeze design proposal into the public engineering contract for the repository. It describes what the project intends to prove. It does not claim that the implementation, tests, benchmarks, or release gates already exist.

## 1. Product thesis

QA Freeze is an offline-first approval-to-handoff verification tool for PNG assets.

The central problem is simple: an artist approves one file, but a later handoff may contain a different revision, a missing asset, a corrupted image, or a file that violates agreed technical rules.

The core promise is:

> **Approve it once. Know when it changes.**

The project focuses on trustworthy preservation and truthful verification rather than image editing.

## 2. Initial user

Primary initial user:

- a technically comfortable indie developer receiving PNG assets from an artist and needing reliable approval verification.

Secondary user:

- a motivated nontechnical artist who can follow a short guided command workflow.

Whether a CLI is usable enough for the secondary user is an empirical question. A thin GUI/installer may become necessary after testing.

## 3. Jobs to be done

QA Freeze should let a user:

1. confirm that a delivered PNG is byte-for-byte identical to the approved PNG;
2. preserve an approved baseline without rewriting the original source;
3. record who approved the asset and optional approval notes;
4. distinguish approval state from integrity state and technical-policy state;
5. detect missing, changed, unreadable, baseline-corrupt, or unapproved files;
6. emit deterministic JSON suitable for automation;
7. emit a readable human summary;
8. keep proprietary artwork local.

## 4. State model

QA Freeze must avoid one ambiguous PASS flag.

At minimum the design distinguishes:

### 4.1 Approval state

Examples:

- approved
- unapproved
- approval metadata missing
- approval record invalid

### 4.2 Source state

Examples:

- present
- missing
- unreadable
- unstable during read

### 4.3 Baseline state

Examples:

- healthy
- referenced object missing
- digest mismatch
- manifest invalid

### 4.4 Byte-integrity state

Examples:

- exact
- changed
- not verifiable

### 4.5 Technical-policy state

Examples:

- compliant
- noncompliant
- not evaluated

A clean handoff requires the relevant dimensions to be clean independently.

## 5. v0.1 scope

### In scope

- PNG source files;
- SHA-256 exact-byte identity;
- explicit approval records;
- content-addressed approved-object storage;
- append-only approval revision records;
- authoritative manifest publication;
- scan/approve/verify CLI;
- deterministic JSON output;
- human-readable console summaries;
- path containment and symlink rejection;
- bounded PNG decoding/security checks;
- crash/interruption tests;
- cross-platform tests on selected Windows/macOS/Linux environments.

### Deferred

- visual/perceptual diff;
- desktop GUI;
- artist-facing HTML report;
- restore command;
- automatic rename detection;
- asset relinking;
- semantic comparison;
- sample-equivalence comparison across differently encoded PNGs.

Deferred work does not delay v0.1 unless its related feature is pulled into scope.

## 6. Proposed CLI contract

Design target:

```text
qa-freeze init ./my-game
cd ./my-game
qa-freeze scan --json scan.json
qa-freeze approve game-assets/ui/start_button.png --note "Approved by art lead"
qa-freeze verify --json verification.json
```

Expected semantics:

- `init` creates QA Freeze state only when explicitly requested.
- `approve` may create immutable approved-object/revision data and publish a new manifest.
- `scan` must not mutate source assets.
- `verify` must not mutate source assets.
- reports must be excluded from future source scans by default.

### Exit codes

- `0` — command completed with no blocking findings.
- `1` — validation completed and found blocking integrity/policy/approval findings.
- `2` — invalid invocation or configuration.
- `3` — filesystem, decoder, or operational failure prevented reliable completion.
- `4` — baseline store or manifest integrity is compromised.

## 7. Proposed storage model

A project has a dedicated QA Freeze state directory.

Conceptual structure:

```text
.qa-freeze/
├── manifest.json
├── objects/
│   └── sha256/
├── revisions/
├── locks/
└── recovery/
```

The exact on-disk schema is a Gate 0 decision and must be versioned.

### Approved objects

Approved bytes should be copied into content-addressed storage keyed by SHA-256.

The copy must be verified after write before the new manifest can reference it.

### Asset identity

`asset_id` should be a UUID independent of filename.

A rename is not automatically inferred in v0.1.

### Revision records

Approval revisions should be append-only records containing enough information to reconstruct:

- asset ID;
- relative source path;
- SHA-256;
- size;
- technical metadata needed by policy;
- approval timestamp;
- approval note when supplied;
- schema version.

## 8. Approval transaction

The design target for approval publication is:

1. obtain a project-scoped single-writer lock;
2. validate source path containment and supported file type;
3. read and hash the source while detecting instability;
4. validate PNG/security limits and technical policy;
5. write approved bytes to a temporary object;
6. sync as appropriate for the platform;
7. publish the content-addressed object;
8. write and validate an append-only revision record;
9. write a temporary manifest referencing only verified objects/revisions;
10. publish the manifest using the strongest measured safe replacement strategy for the platform;
11. release the lock.

The manifest is the commit marker.

An interruption before manifest publication may leave an unreferenced object or revision. That is acceptable evidence. The previous valid manifest remains authoritative.

Automatic deletion of orphan evidence is outside v0.1.

## 9. Concurrency

Two approving processes must never silently lose revisions.

v0.1 should serialize approvals using a tested project lock.

A process that cannot obtain the lock before its timeout fails cleanly.

A verifier should read one complete manifest snapshot and independently verify referenced objects.

No claim is made that this prevents filesystem tampering by an attacker controlling the local account.

## 10. Filesystem rules

Required test cases include:

- spaces and Unicode paths;
- case-collision behavior;
- Unicode-normalization collisions;
- read-only directories;
- permission failures;
- symlinks;
- junctions where applicable;
- path traversal attempts;
- special files;
- file replacement or modification while being read.

Reads must stay inside the authorized project root.

Symlink traversal and special-file escape should be rejected.

Network shares and unusual/removable filesystems remain best-effort or unsupported for durability claims until measured.

## 11. PNG handling

v0.1 verifies exact bytes independently from technical PNG policy.

The implementation must defend against malformed or pathological files with:

- PNG signature validation;
- bounded input size;
- bounded decoded dimensions/pixel count;
- bounded metadata where practical;
- Pillow decompression-bomb controls;
- warnings promoted to blocking findings where reliable interpretation is impossible.

A decoder crash or unbounded memory condition is a release blocker.

16-bit/sample-equivalence behavior must be explicit. Unsupported semantic comparison is reported as `not_evaluated`, never silently treated as equal.

## 12. Security and confidentiality

Assets are confidential by default.

Core code:

- requires no network access;
- sends no telemetry;
- does not embed image bytes in default reports;
- uses relative paths in public/shareable reports;
- excludes EXIF/freeform embedded metadata from default machine reports unless the contract explicitly requires it;
- never stores credentials in reports.

The security boundary is operational mistakes and malformed local files, not a malicious administrator controlling the machine.

See [docs/threat-model.md](docs/threat-model.md).

## 13. Reporting

### JSON

The JSON contract must be schema-versioned and stable enough for automation.

Reports should describe independent states rather than flatten findings into one flag.

### Human output

Console output should identify:

- asset;
- approval status;
- source state;
- baseline state;
- exact-byte state;
- technical-policy state;
- blocking findings;
- next useful action.

Future HTML reports must escape all user-controlled strings and contain no network assets or executable user content.

## 14. Performance targets

Initial targets are hypotheses to measure, not guarantees.

Suggested Gate 2 benchmark fixture:

- 1,000 synthetic static PNGs;
- approximately 2 GiB total;
- corrupt/pathological metadata cases;
- large-dimension cases;
- unusual valid filenames.

Initial measurement targets on the declared reference SSD system:

- single-process verification <= 90 seconds;
- peak process memory < 256 MiB for supported inputs;
- zero source mutation;
- deterministic classification.

Failure to hit a target requires documentation and rebaselining before release.

## 15. Release-blocking behavior tests

Gate 2 should include at least the following categories:

1. approved exact file verifies exact;
2. one-byte change reports changed;
3. missing source reports missing;
4. corrupt/unreadable PNG reports a blocking finding;
5. unapproved file remains distinguishable from changed approved file;
6. referenced baseline object missing is a baseline-integrity failure;
7. referenced object digest mismatch is a baseline-integrity failure;
8. source scanner does not mutate source bytes or metadata;
9. approval creates a verified baseline revision;
10. repeated approval of a changed file preserves prior revision history;
11. malformed metadata is bounded/rejected;
12. unsupported high-bit-depth semantic comparison is explicit `not_evaluated`;
13. decompression-bomb/oversize input is rejected within bounded resources;
14. interrupted approval before manifest publication leaves old manifest authoritative;
15. simultaneous approvals serialize or fail cleanly;
16. traversal/symlink/junction/special-file escape is rejected;
17. changed-during-read aborts rather than approving mixed bytes;
18. case/Unicode-normalization path collision is explicit;
19. read-only scan/verify does not mutate source files;
20. clean installation and independent JSON parsing match documentation.

Every release-blocking test must pass on the supported CI matrix.

## 16. Supply chain

Before public executable release:

- define supported Python versions;
- use `pyproject.toml`;
- maintain a reproducible/pinned development environment;
- inventory dependencies;
- review dependency licenses;
- pin GitHub Actions or equivalent workflow dependencies;
- use least-privilege CI permissions;
- checksum/sign release artifacts where feasible;
- document offline installation/use expectations.

## 17. Ownership and public/private separation

The public repository may contain:

- original code;
- synthetic fixtures;
- schemas;
- documentation;
- tests;
- security/contribution policies;
- public-safe example reports;
- changelog.

It must exclude:

- commercial/private game code;
- proprietary game art;
- creative-client data;
- internal prompts;
- credentials;
- personal documents;
- private repository history;
- private connector/account dependencies.

## 18. User validation

Release success is deliberately separate from GitHub popularity.

Initial external validation target:

- recruit 5–10 independent target users;
- have them perform approve → alter → verify using disposable/sample PNGs;
- require at least 80% unaided completion after the README;
- require at least four participants to report that the workflow addresses a real handoff problem;
- record errors and language misunderstandings;
- do not collect proprietary user artwork.

If the CLI creates more friction than it removes, investigate a thin GUI/installer or stop expansion.

## 19. Maintenance gate

Do not publish a maintained executable release without:

- an accountable technical maintainer;
- a security reporting path;
- issue triage ownership;
- a release checklist;
- semantic versioning;
- dependency/license review;
- a policy for schema deprecation/migration.

## 20. Decision

The public specification is approved for publication.

Implementation proceeds through gated evidence:

- Gate 0 — architecture/data contracts/threat model/fixtures.
- Gate 1 — bounded technical proof.
- Gate 2 — CLI, policy, JSON contract, cross-platform release-blocking tests.
- Gate 3 — outside-user validation.
- Gate 4 — security/license/package/release review.

No gate is considered passed because this document exists.
