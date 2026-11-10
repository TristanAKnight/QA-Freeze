# Roadmap

## Gate 0 — Architecture review

Deliver:

- architecture decisions;
- manifest/object/revision schema;
- explicit filesystem tradeoffs;
- threat model review;
- synthetic fixture plan;
- supported-platform assumptions.

Exit evidence:

- reviewable ADRs;
- schema draft;
- accepted constraints;
- revised prototype estimate.

## Gate 1 — Technical proof

Deliver:

- SHA-256 exact-file verification;
- approved-object store;
- approval revision records;
- crash-safe manifest publication;
- missing/changed/exact classification;
- fault-injection tests for interrupted approval.

Exit evidence:

- live demonstration;
- exact/changed/missing behavior proven;
- old manifest survives interrupted publication.

## Gate 2 — Public alpha readiness

Deliver:

- CLI contract;
- JSON schema;
- technical-policy checks;
- path/symlink protections;
- concurrency lock;
- cross-platform CI;
- T01–T20 release-blocking tests;
- benchmark report;
- clean installation documentation.

Exit evidence:

- 100% release-blocking tests pass on supported matrix;
- no critical/high unresolved security finding;
- no source-mutation regression.

## Gate 3 — User validation

Deliver:

- 5–10 independent usability sessions;
- anonymized completion/error notes;
- comparison against baseline checksum/Git workflow;
- prioritized fixes.

Proceed target:

- >=80% unaided completion after README;
- no false exact matches;
- >=4 users report a real workflow benefit.

## Gate 4 — Release review

Deliver:

- security review;
- dependency/license inventory;
- packaging review;
- release checklist;
- verified public artifacts;
- maintainer/security contact readiness.

Only then decide whether to publish a maintained executable release.
