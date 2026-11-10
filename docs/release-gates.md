# Release Gates

QA Freeze separates public specification publication from executable software release.

## G0 — Architecture

**Owner:** engineering lead + creative/product reviewer.

Evidence:

- ADRs;
- storage/schema proposal;
- threat model;
- synthetic fixture design;
- explicit filesystem constraints;
- revised implementation estimate.

Decision: prototype / revise / stop.

## G1 — Proof of concept

Evidence:

- exact approved bytes verify exact;
- altered bytes verify changed;
- absent source verifies missing;
- baseline object integrity is rechecked;
- interrupted approval cannot publish a partial new authoritative state.

Decision: continue / stop.

## G2 — Public alpha

Evidence:

- documented CLI;
- stable/versioned JSON contract;
- exit-code contract;
- cross-platform CI;
- T01–T20 release-blocking tests;
- clean-install test;
- benchmark report;
- security review of v0.1 attack surface.

Quality requirement:

- 100% release-blocking behavior tests pass on supported OS matrix;
- no critical/high unresolved security findings;
- no baseline-integrity regression;
- no unintended source mutation.

## G3 — User validation

Evidence:

- 5–10 independent target users;
- explicit success/failure summary;
- no proprietary participant assets collected.

Proceed target:

- >=80% unaided completion;
- zero false exact matches;
- >=4 users report a real handoff benefit.

Decision: beta / revise / pivot / stop.

## G4 — Release

Evidence:

- license/dependency review;
- packaging review;
- security reporting path;
- release checklist;
- changelog/version;
- checked public artifacts;
- accountable maintainer.

Decision: release / defer.

## Important

A GitHub repository existing does not pass any gate.

A well-written specification does not prove implementation competence.

Stars, forks, or page views are not substitutes for user-task success.
