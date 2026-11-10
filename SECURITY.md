# Security Policy

QA Freeze handles files that may be proprietary. Privacy and integrity failures therefore matter even in a local CLI.

## Supported security scope

The intended v0.1 security boundary covers:

- accidental or operational file changes;
- malformed PNG input;
- path traversal and symlink/junction escape;
- corrupted baseline data;
- interrupted approval transactions;
- approval concurrency;
- unsafe report serialization;
- dependency/supply-chain hygiene.

QA Freeze does **not** claim to resist an attacker who controls the user account, operating system, hardware, or both the baseline and source storage.

## Reporting a vulnerability

Do not post credentials, private artwork, exploit payloads containing confidential files, or private repository contents in a public issue.

If GitHub Private Vulnerability Reporting / Security Advisories are enabled for this repository, use that channel.

If no private reporting channel is available, open a public issue containing only:

- a short statement that you found a potential security issue;
- affected version/commit when known;
- a request for a private contact path.

Do not include working exploit details until a private channel is established.

## Security expectations for contributors

Contributions must preserve:

- project-root containment;
- no symlink traversal for asset reads;
- bounded resource use for decoded images/metadata;
- atomic-or-recoverable approval publication;
- no network requests in core verification code;
- no telemetry in core verification code;
- no absolute/private paths in shareable reports;
- correct escaping of user-controlled text;
- no source mutation during scan/verify.

## Private test material

CI must use synthetic/public-safe fixtures only.

Never upload real proprietary game art as:

- test fixtures;
- GitHub Actions artifacts;
- issue attachments;
- PR attachments;
- benchmark data.

## Dependency policy

Before executable release:

- review direct dependency licenses;
- pin supported versions appropriately;
- use least-privilege GitHub Actions permissions;
- pin third-party actions to reviewed revisions where practical;
- keep decoder/security dependencies patched.

## Response target

Security reports should be triaged when a maintainer is available. Any response-time target is a maintenance goal rather than a guaranteed SLA until the project has formal support staffing.
