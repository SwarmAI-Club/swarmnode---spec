# Roadmap

The project opens in stages. Each stage has an explicit gate; nothing moves forward on optimism alone.

## Phase 0 — Specification (now)

- Publish architecture, protocol, installer guide and lesson schema.
- Collect review comments from node operators and security reviewers.
- No public swarm, no implementation code in this repository.

**Gate to Phase 1:** no unresolved protocol-level objection; at least two external reviewers have read the lifecycle and ACL model.

## Phase 1 — Reference installer (weeks 1–6)

- Publish `swarmnode-installer` (PowerShell + WSL scripts, Apache-2.0).
- Publish a self-hosted swarm guide with a Compose file.
- Secret scan, dependency audit, reproducible reference build.
- Three external users complete a self-hosted install unaided.

**Gate to Phase 2:** install succeeds from a clean Windows 11 machine; no credentials or internal addresses in the repository; installer logs contain no secrets.

## Phase 2 — Core implementation (weeks 6–12)

- Open `swarmnode-core`: agent-core, MQTT bridge, dispatcher, knowledge gate.
- Signed releases and SBOM.
- Invite-only developer swarm (3–20 nodes).
- 30-day soak with lease/retry, LWT and lesson-gate tests.

**Gate to Phase 3:** 20+ nodes, 30 days without data loss or repeatable crash, independent security review, documented incident response.

## Phase 3 — Federation

- Public registration with operator approval, quotas and reputation.
- Multiple swarms federate; a node can join more than one swarm.
- Contribution accounting by reputation and credits.

**Explicit non-goal:** a financial token. If that ever changes, it requires legal review and a separate design document; it is not on this roadmap.

## What stays private

- Broker operations and hosted relay.
- Billing, support portal and customer records.
- Signing keys for official lesson packs.
- Model weights (upstream licences; downloaded, not redistributed).

## How to influence the roadmap

Open an issue with the `rfc` label. Proposals need: problem, alternatives, impact on privacy, impact on operators, and a rollback plan.
