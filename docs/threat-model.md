# Threat Model

## Assets to protect

- approved PNG bytes;
- approval history;
- authoritative manifest state;
- private source paths;
- private artwork;
- integrity findings;
- local approval notes.

## Trust boundary

QA Freeze is a local developer/artist tool.

It is designed to reduce accidental and operational mistakes and to reject malformed/untrusted file input safely.

It is not designed to defeat an attacker with administrator/root control over the machine or an attacker able to rewrite both the source and QA Freeze state.

## Threats

### Private file disclosure

Mitigations:

- local/offline operation;
- relative paths in shareable reports;
- no image embedding by default;
- no EXIF/freeform metadata in default reports unless contractually needed;
- no telemetry.

Residual risk:

A user can still deliberately share a private asset or report.

### Path traversal and symlink/junction escape

Mitigations:

- canonical project-root containment checks;
- reject traversal outside authorized root;
- no symlink traversal;
- reject unsupported special files;
- test Windows junction/reparse behavior separately.

Residual risk:

Concurrent filesystem changes cannot be eliminated completely.

### PNG decompression bombs / pathological metadata

Mitigations:

- input-size limits;
- decoded pixel/dimension limits;
- metadata limits;
- Pillow safety controls;
- warnings promoted to failure when reliable interpretation is impossible.

Residual risk:

Decoder vulnerabilities require upstream patching. QA Freeze is not a hardened multi-tenant sandbox.

### Manifest/baseline manipulation

Mitigations:

- schema validation;
- content-addressed objects;
- rehash referenced objects;
- atomic/recoverable publication;
- append-only approval revisions;
- backup guidance.

Residual risk:

A malicious local administrator can modify both baseline and program state.

### Interrupted approval

Mitigations:

- single-writer project lock;
- write temporary object/revision first;
- verify;
- publish manifest last as commit marker;
- previous manifest remains authoritative before publication.

Residual risk:

Power-loss guarantees vary by filesystem/OS and must be measured.

### Concurrent approval

Mitigations:

- advisory project lock with timeout;
- clean conflict instead of silent overwrite.

### Changed-during-read asset

Mitigations:

- detect unstable read/stat/hash conditions;
- abort rather than approve mixed bytes.

### Malicious filenames/notes

Mitigations:

- JSON serialization;
- future HTML escaping;
- no dynamic script insertion;
- future restrictive CSP.

### Supply-chain compromise

Mitigations:

- dependency review;
- version pinning/locking;
- least-privilege CI;
- pinned third-party actions where practical;
- release artifact checksums/signatures where feasible.

Residual risk:

No package ecosystem provides absolute upstream safety.

## Privacy acceptance criteria

Core code must not make unapproved network requests.

Default machine-readable reports must not contain:

- absolute private paths;
- embedded source assets;
- EXIF;
- arbitrary embedded image metadata;
- credentials.

CI uses synthetic assets only.
