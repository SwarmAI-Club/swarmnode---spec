# Governance

## Maintainers

The project starts with a single maintainer (the SwarmNode author). Maintainer status is added after sustained, high-quality contributions and is recorded in `MAINTAINERS.md`.

## Decisions

- Small changes: pull request with a clear description.
- Protocol changes: an RFC issue (`rfc` label) open for at least 7 days.
- Security-sensitive changes: private review before merge.
- Lazy consensus: silence is not approval for protocol changes; at least one maintainer must approve.

## Contribution sign-off

Every commit must carry a Developer Certificate of Origin sign-off:

```
Signed-off-by: Your Name <you@example.com>
```

Use `git commit -s`. We do not require a CLA; contributions are accepted under the repository licence.

## Releases

- Semantic versioning for code; protocol versions are separate (`v1`, `v2`).
- Releases are tagged and signed.
- The changelog states protocol compatibility and migration notes.

## Code of conduct

See [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md).
