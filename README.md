# SwarmNode — Open Specification

SwarmNode is a low-cost home edge AI computer: a small Windows machine connected to the TV that runs a local language model, a 24/7 agent, and an ant-style knowledge network. This repository holds the **open protocol specification, reference architecture and installer guide**.

- Website: <https://swarmnode.space>
- Contact: <support@swarmnode.space>
- Status: **soft-open (specification only)** — no implementation code and no public swarm yet. See [docs/roadmap.md](docs/roadmap.md).

## Why spec-first

The value of the network is trust: users must be able to verify what leaves the device, how tasks are leased, and how shared lessons are gated. Publishing the specification first lets reviewers and early node operators challenge the design before code is frozen.

## Scope

This repository covers **Part II** of SwarmNode:

- device-owned memory, experience, harness tuning and LLM upgrades;
- the MQTT task fabric (presence, heartbeat, shared queue, lease/retry, pheromone);
- the signed-lesson knowledge gate;
- the first application: sanitized smart-device recipes.

It does **not** cover SwarmAI's GPU compute marketplace, and it does not include model weights, credentials or customer data.

## Contents

| Path | What |
|---|---|
| [docs/applications.md](docs/applications.md) | First application, flywheel, what comes later |
| [docs/architecture.md](docs/architecture.md) | Components, trust boundaries, data flow |
| [docs/protocol.md](docs/protocol.md) | MQTT v1 topics, payloads, lifecycle, ACL |
| [docs/installer.md](docs/installer.md) | Reference install on Windows 11 + WSL2 |
| [docs/roadmap.md](docs/roadmap.md) | Phases, gates, what opens when |
| [docs/governance.md](docs/governance.md) | Maintainers, RFCs, DCO |
| [knowledge/schema/lesson.schema.json](knowledge/schema/lesson.schema.json) | Signed lesson format |
| [docs/decisions/0001-license.md](docs/decisions/0001-license.md) | Why Apache-2.0 |

## Principles

1. **Local first** — inference, memory and raw data stay on the device by default.
2. **No profile required** — the device works without an account. Accounts exist only for optional services.
3. **Verified sharing** — a lesson only takes effect after the receiving device passes it through its own golden tests.
4. **Bounded promises** — "lifetime" means the supported life of the device, not unlimited labour.
5. **No token** — contribution is tracked by reputation and credits, not a speculative asset.

## Licensing

- Code and schemas: [Apache-2.0](LICENSE).
- Documentation: CC-BY-4.0 unless stated otherwise.
- Model weights: follow their upstream licences; they are never redistributed here.
- The SwarmNode name and logo are governed by [TRADEMARK.md](TRADEMARK.md).

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md). Security reports go to <support@swarmnode.space>, not the public issue tracker.
