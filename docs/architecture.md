# Architecture

## What the device is

A SwarmNode is a low-cost mini PC (reference: Intel N150, 12 GB RAM, 512 GB SSD) running Windows 11 with WSL2. It sits under the TV and serves three roles:

1. **Home computer** — browser on the TV, music, video, smart-home dashboard.
2. **Always-on agent host** — local LLM, speech input/output, Telegram interface, 24/7 automation.
3. **Ant node** — optional participation in a swarm: accepting leased tasks, publishing presence, sharing gated lessons, including sanitized device recipes.

## Components

```
┌──────────────────────────── Device (private) ────────────────────────────┐
│                                                                           │
│  Windows 11                                                               │
│   ├── Chrome / Edge ── TV (HDMI)                                          │
│   ├── MT5 terminal (optional)                                             │
│   └── WSL2 (Ubuntu)                                                       │
│        ├── litert-lm serve        127.0.0.1:8080   local LLM              │
│        ├── agent-core             127.0.0.1:8090   intent, memory, tools  │
│        ├── telegram bot           outbound only                           │
│        ├── mqtt_bridge            outbound TLS 8883                       │
│        ├── knowledge gate         local files + golden tests              │
│        └── (optional) mqtt_dispatcher  127.0.0.1:8091  one per swarm      │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                    │ TLS 1.2+ (MQTT)
                    ▼
        ┌───────────────────────────┐
        │ Broker (self-hosted or    │
        │ operator-hosted)          │
        │ - per-node credentials    │
        │ - topic ACL               │
        │ - retained presence, LWT  │
        └───────────────────────────┘
```

## Trust boundaries

| Boundary | Rule |
|---|---|
| Device ↔ internet | Only the bridge and bot make outbound connections. No inbound ports required. |
| Device ↔ broker | TLS, unique credentials, ACL limited to the node's own topics plus the shared queue. |
| Device ↔ device | No direct connection. All coordination goes through broker topics. |
| Raw data ↔ swarm | Raw audio, video, documents and chat logs never leave the device. Only signed lessons and task text/results cross the boundary. |
| Lesson ↔ activation | A lesson is inert until the local gate verifies signature, scope, expiry and golden-test improvement. |
| Device recipe ↔ actuation | A recipe is advisory. It never runs on unmatched hardware, and high-risk actions always require explicit operator confirmation. |

## Data flow: a task

1. A controller submits `{text, count, timeout, lease, max_attempts}` to its local dispatcher.
2. The dispatcher assigns a task id, publishes it to `<swarm>/v1/tasks/queue` with `lease_until`.
3. One ant receives it via shared subscription and immediately publishes `accepted`.
4. The ant runs the task locally (agent or LLM mode) and publishes a result.
5. The dispatcher accepts the first result, records latency, and updates pheromone indirectly through node heartbeats/status.
6. If no result arrives before the lease expires, the dispatcher re-queues with `attempt + 1`, up to `max_attempts`.

## Data flow: a lesson

1. An author writes a structured lesson and signs it with an Ed25519 key.
2. The lesson enters `pending/` on a device.
3. The gate checks signature, trust level, expiry, PII and injection patterns.
4. The gate runs the local golden set with and without the lesson.
5. Only if the candidate is not worse does the lesson move to `adopted/` and become active.

## Data flow: a device recipe

1. An operator integrates a device locally (vendor app, Home Assistant, Matter, MQTT or HTTP).
2. The agent drafts a `device_recipe`: category, vendor, model, protocol, firmware band, integration, generic steps, verification and risk.
3. The sanitizer strips local identifiers: addresses, hostnames, SSIDs, entity IDs, room and account names, tokenised URLs.
4. The operator reviews and publishes; the node signs the recipe.
5. A receiving node verifies signature, trust, scope, expiry and PII, then matches category, vendor, model, protocol, firmware and adapter.
6. It runs the recipe's `verification` locally. Only an observable pass is adopted; no match stays `untested/` and inert.
7. Low-risk recipes may then run automatically after the gate. Medium risk needs a first-run confirmation. High risk needs confirmation on every actuation.

The recipe shares **how to integrate a model of device**, never the operator's device identity, layout or credentials. This is the colony effect: one person's work becomes another person's starting point, without pooling private data.

## Deployment profiles

| Profile | Broker | Dispatcher | Use |
|---|---|---|---|
| Solo | none | none | Device works fully offline. |
| Self-hosted swarm | user's Mosquitto | one node | Families, labs, small co-ops. |
| Operator swarm | operator | operator | Invite-only dev swarm; later public with quotas. |

## Non-goals

- Distributed tensor-parallel inference across homes.
- Sharing raw sensor data, recordings or browsing history.
- Redistributing model weights.
- A financial token in the current design.
