# Contributing

Thanks for helping. This repository is specification-first, so documentation and protocol review are as valuable as code.

## Before you start

- Read [docs/architecture.md](docs/architecture.md) and [docs/protocol.md](docs/protocol.md).
- For anything larger than a typo, open an issue first. Protocol changes need an `rfc` issue.
- Never include credentials, private keys, customer data, internal hostnames or model weights.

## Pull requests

1. Fork and branch from `main`.
2. Keep the change focused; one concern per pull request.
3. Update the relevant document and the changelog.
4. Sign off your commits: `git commit -s`.
5. Describe the privacy and operational impact.

## What we cannot accept

- Code or data that requires redistributing model weights.
- Scraped personal data, chat logs or recordings.
- Changes that make the default configuration send raw data off-device.
- Features that depend on a financial token.
- Secrets of any kind, including test credentials that look real.

## Style

- Documentation: Traditional Chinese or English, consistent within a file.
- Code (when repositories open): formatted, tested, no dead code, no commented-out secrets.
- Commit messages: imperative mood, explain *why*.

## Review

Maintainers review for correctness, threat model and operator burden. A change that works but makes self-hosting harder will be questioned.

## Security

Do not open a public issue for a vulnerability. See [SECURITY.md](SECURITY.md).
