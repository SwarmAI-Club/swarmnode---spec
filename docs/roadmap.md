# Roadmap

The project opens in stages. Each stage has an explicit gate; nothing moves forward on optimism alone.

Last updated: 2026-10-22.

## Status at a glance

| Item | State | Where |
|---|---|---|
| Specification (this repo) | ✅ written, local commit | `/mnt/d/swarmnode-spec` |
| GitHub organisation and repository name | ✅ `SwarmAI-Club/swarmnode---spec` (rename recommended) | GitHub |
| Public push of this repo | ✅ 2026-10-22 | [GitHub](https://github.com/SwarmAI-Club/swarmnode---spec) |
| CI (link check, JSON schema, secret scan) | ⏳ | — |
| Two external reviewers | ⏳ | — |
| Reference installer | ⏳ Phase 1 | — |
| Self-hosted swarm compose + ACL generator | ⏳ Phase 1 | — |
| Hardware BOM | ⏳ Phase 1–2 | — |
| Device recipe schema + example | ✅ | `knowledge/` |
| Recipe sanitizer + adapter registry | ⏳ Phase 2 | — |
| Core implementation (Apache-2.0) | ⏳ Phase 2 | — |
| Invite-only developer swarm | ⏳ Phase 2 | — |
| Public registration / federation | ⏳ Phase 3 | — |

## Repository plan

| Repository | Contents | Licence | Opens |
|---|---|---|---|
| `swarmnode-spec` | This repository: architecture, protocol, installer guide, lesson schema | Apache-2.0 + CC-BY-4.0 docs | Phase 0 |
| `swarmnode-installer` | PowerShell bootstrap, WSL setup, service templates, self-hosted Compose | Apache-2.0 | Phase 1 |
| `swarmnode-knowledge` | Lesson schema, gate reference, example lessons | Apache-2.0 + CC-BY-4.0 | Phase 1 |
| `swarmnode-hardware` | BOM, enclosure notes, port/power spec; solar version as spec only | CERN-OHL-S or CC-BY-4.0 (decide at Phase 1) | Phase 1–2 |
| `swarmnode-core` | agent-core, MQTT bridge, dispatcher, knowledge gate | Apache-2.0 | Phase 2 |
| Private operations | Broker, relay, billing, support portal, signing keys, customer records | proprietary | never |

Rules: no model weights, no credentials, no customer data, no internal addresses in any public repository.

## Phase 0 — Specification (current)

Deliverable: a reviewable protocol and architecture.

- [x] Architecture, trust boundaries and deployment profiles.
- [x] Protocol v1: topics, payloads, lease/retry, ACL, liveness.
- [x] Installer guide and lesson schema.
- [x] Repository created under `SwarmAI-Club` (name has three hyphens; rename to `swarmnode-spec` recommended).
- [x] Secret scan and publish: https://github.com/SwarmAI-Club/swarmnode---spec
- [ ] Add CI: markdown link check, JSON schema validation, `gitleaks`.
- [ ] Two external reviewers read the lifecycle and ACL model.
- [ ] Resolve any protocol-level objections before freezing v1.

**Gate to Phase 1:** repository is public, CI is green, and at least two external reviewers have signed off on the lifecycle and ACL model.

## Phase 1 — Reference installer (target 2026-12-03)

Deliverable: a stranger can install a self-hosted swarm from a clean Windows 11 machine.

- [ ] Create `swarmnode-installer` (Apache-2.0).
- [ ] PowerShell bootstrap: WSL2, Ubuntu 24.04, Python venv, directories, service user.
- [ ] WSL setup: runtime install, model download with SHA-256 check, `agent.env` / `mqtt.env` with `0600`.
- [ ] Compose file for a self-hosted Mosquitto broker with TLS.
- [ ] ACL generator: one credential per node, controller-only queue writes.
- [ ] Service templates: agent, bridge, optional dispatcher, restart policy, log hygiene.
- [ ] Publish `swarmnode-knowledge` with the schema, sanitizer rules and non-sensitive examples, including a `device_recipe`.
- [ ] Publish `swarmnode-hardware` BOM and power/port notes (no solar build instructions).
- [x] Device recipe schema: `kind`, `device`, `risk`, `steps`, `verification` and a sanitized example.
- [ ] Sanitizer rules and fixtures: addresses, hostnames, entity IDs, room/account names, tokens.
- [ ] Run `gitleaks`, dependency audit and a reproducible reference build.
- [ ] Three external users complete the install unaided; record every failure.

**Gate to Phase 2:** install succeeds from a clean Windows 11 machine; no credentials or internal addresses in the repository; installer logs contain no secrets; three external installs recorded.

## Phase 2 — Core implementation (target 2027-01-14)

Deliverable: the reference implementation is public and a small developer swarm runs on it.

- [ ] Open `swarmnode-core`: agent-core, MQTT bridge, dispatcher, knowledge gate.
- [ ] Unit tests for task lifecycle, lease expiry, dedupe and lesson gate.
- [ ] Integration test with a local broker (TLS, ACL, LWT, shared subscription).
- [ ] SBOM and signed releases with checksums.
- [ ] Adapter registry: Home Assistant, Tuya, Matter, MQTT, HTTP; device matching by category/vendor/model/protocol/firmware.
- [ ] Recipe verification harness and risk policy: low auto after gate, medium first-run confirmation, high every-actuation confirmation.
- [ ] Recipe transport decision: repository-only vs a broker gossip topic with revocation and replay protection.
- [ ] Reproduction tracking for reputation (no token, no raw data).
- [ ] Invite-only developer swarm, 3–20 nodes.
- [ ] 30-day soak: lease/retry, LWT, lesson gate, no data loss.
- [ ] Independent security review; publish a summary.
- [ ] Incident response runbook.

**Gate to Phase 3:** 20+ nodes, 30 days without data loss or repeatable crash, independent security review closed, runbook published.

## Phase 3 — Federation (2027-Q1 or later)

Deliverable: independent swarms can interoperate without a central account.

- [ ] Operator-approved public registration with quotas.
- [ ] Reputation and contribution credits (no token).
- [ ] Federation: a node may join more than one swarm.
- [ ] Abuse handling: rate limits, revocation, takedown process.
- [ ] Cross-swarm recipe exchange with provenance, revocation and abuse handling.
- [ ] Privacy review of any new metadata.

**Explicit non-goal:** a financial token. If that ever changes, it requires legal review and a separate design document; it is not on this roadmap.

## Definition of done (every phase)

- Documented: a new operator can follow the docs without asking the author.
- Tested: the gate is automated, not a manual opinion.
- Private by default: no raw data leaves the device unless the operator opts in.
- Reversible: every migration has a rollback path.
- Colony value: a second node can reproduce a shared device integration without receiving private data.
- No secrets in git, logs or releases.

## What stays private

- Broker operations and hosted relay.
- Billing, support portal and customer records.
- Signing keys for official lesson packs.
- Model weights (upstream licences; downloaded, not redistributed).

## How to influence the roadmap

Open an issue with the `rfc` label. Proposals need: problem, alternatives, impact on privacy, impact on operators, and a rollback plan.
