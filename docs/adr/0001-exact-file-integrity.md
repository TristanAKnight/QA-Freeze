# ADR 0001 — Exact-file integrity is the primary v0.1 verification primitive

**Status:** Accepted for prototype  
**Date:** November 2026

## Context

The originating workflow needs to answer a narrow question reliably:

> Is this exact PNG file the file that was approved?

Image appearance alone cannot answer that question. Re-encoding, metadata changes, accidental replacement, or wrong revisions may produce files that look similar while differing in bytes.

At the same time, exact-byte identity does not answer whether the file is visually correct, technically compliant, or explicitly approved by a human.

## Decision

QA Freeze v0.1 uses SHA-256 as the exact-file identity primitive.

The system reports exact-byte integrity separately from:

- approval state;
- source availability/readability;
- baseline health;
- technical PNG policy.

Approval copies are preserved without re-encoding.

## Consequences

Positive:

- deterministic exact identity;
- simple independently verifiable primitive;
- no decoder needed for byte-integrity comparison;
- suitable for content-addressed approved-object storage.

Limits:

- semantically identical images encoded differently are byte-different;
- byte equality does not prove aesthetic approval unless linked to an approval record;
- SHA-256 does not protect against a malicious administrator who can rewrite both baseline and software state.

## Deferred decision

Perceptual/sample-equivalence comparison may be explored after v0.1 reliability is proven. It must never replace exact-byte status; it would be an additional independent state.
