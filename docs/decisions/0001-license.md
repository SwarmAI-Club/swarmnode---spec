# 0001 — Licence choice

- Status: accepted
- Date: 2026-10-22

## Context

SwarmNode's code and protocol should be reviewable and forkable. The business is hardware, onboarding, support and a verified knowledge network — not secrecy of the client code. The project also wants contributions from individual node operators, not only companies.

## Decision

- **Code and schemas:** Apache-2.0.
- **Documentation and lesson examples:** CC-BY-4.0.
- **Name and logo:** trademark policy, separate from the code licence.
- **Model weights:** not distributed here; upstream licences apply.
- **Hosted services** (broker operations, billing, support): proprietary and out of scope.

## Why Apache-2.0, not AGPL-3.0

| Criterion | Apache-2.0 | AGPL-3.0 |
|---|---|---|
| Adoption by individuals and schools | high | low |
| Patent grant | explicit | limited |
| Prevents closed hosted forks | no | yes |
| Matches a hardware/support business | yes | possible, but discourages operators |

A copyleft licence would protect against a hosted clone, but the current risk is obscurity, not cloning. If a hosted clone becomes a real problem, the project can revisit with a dual-licence model, which requires owning all copyright (a CLA) — not viable in Phase 0.

## Consequences

- Competitors may reuse the code. The moat is the device, support, brand and the verified lesson network.
- The trademark policy prevents modified builds from presenting themselves as official.
- Third-party dependencies keep their own licences; a dependency audit is required before Phase 2.
- Model weights must never be committed or re-licensed by this project.
