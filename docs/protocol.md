# Swarm Protocol v1

Status: **draft, deployed in a 3-node private test**. Breaking changes bump the version segment to `v2`.

## Transport

- MQTT 3.1.1 over TLS 1.2+ (port 8883 by convention).
- Anonymous access is disabled.
- Each node has its own username and password; the broker ACL restricts it to its own topics and the shared queue.
- Payloads are UTF-8 JSON. Payload-level signatures are **not yet implemented**; transport security is TLS plus broker ACL.

## Namespace

All topics are prefixed with a swarm id:

```
<swarm_id>/v1/...
```

`<swarm_id>` matches `^[a-z0-9-]{2,32}$`. The reference test swarm uses `swarm`. A swarm id namespaces topics and ACLs; it is not a secret.

## Identity

| Field | Meaning |
|---|---|
| `NODE_ID` | Stable node name, `^[a-z0-9-]{2,32}$`, unique within the swarm. |
| Broker username | Usually equal to `NODE_ID` (implementation detail). |
| Ed25519 key | Used to sign lessons. Public key is shared; private key stays on the device (`0600`). |

## Topics

| Topic | QoS | Retain | Publisher | Subscribers |
|---|---|---|---|---|
| `<swarm>/v1/nodes/<id>/status` | 1 | yes | node | everyone |
| `<swarm>/v1/nodes/<id>/heartbeat` | 0 | no | node | controller, observers |
| `<swarm>/v1/pheromone/<id>` | 0 | yes | node | everyone |
| `<swarm>/v1/tasks/queue` | 1 | no | dispatcher only | ants via shared subscription |
| `<swarm>/v1/tasks/<task_id>/accepted` | 0 | no | ant | dispatcher |
| `<swarm>/v1/tasks/<task_id>/result` | 1 | no | ant | dispatcher |
| `<swarm>/v1/broadcast/<topic>` | 1 | no | controller | reserved |

Ants subscribe to the shared queue:

```
$share/ants/<swarm>/v1/tasks/queue
```

The shared subscription guarantees that one task is delivered to one ant in the group, not to every ant.

## Payloads

### status (retained, LWT)

```json
{
  "id": "queenbee-1",
  "online": true,
  "model": "gemma-4-e2b",
  "runtime": "litert-lm",
  "endpoint": "",
  "swarm": "swarm",
  "version": 2,
  "ts": 1790000000.0
}
```

The broker publishes the same payload with `"online": false` as the last will when the connection drops. A retained status older than the heartbeat window must not be treated as proof of liveness.

### heartbeat (every 30 s)

```json
{
  "id": "queenbee-1",
  "ts": 1790000000.0,
  "load": 0.42,
  "mem_free_mb": 3120,
  "tasks": 17,
  "busy": false,
  "queued": 0
}
```

Liveness rule: a node is online when it has a retained `online: true` status **and** a heartbeat younger than 120 s.

### pheromone (retained)

```json
{
  "id": "queenbee-1",
  "score": 0.94,
  "ema_latency": 5.8,
  "ok": 47,
  "fail": 3,
  "ts": 1790000000.0
}
```

`score = ok / (ok + fail)`, `0` when there is no history. `ema_latency` is an exponential moving average of task latency in seconds.

### task (queue)

```json
{
  "id": "d1a2b3c4d5",
  "text": "turn on the living room light",
  "attempt": 1,
  "lease_until": 1790000120.0,
  "timeout": 90,
  "ts": 1790000000.0
}
```

| Field | Notes |
|---|---|
| `id` | `d` + 10 hex chars. Unique per submission. |
| `attempt` | Starts at 1. |
| `lease_until` | Unix seconds. Ants must skip the task if this is already in the past. |
| `timeout` | Seconds. The ant may reduce it to fit inside the lease. |

### accepted

```json
{
  "id": "d1a2b3c4d5",
  "node": "queenbee-1",
  "status": "accepted",
  "ts": 1790000001.0
}
```

`status` may be `accepted` or `expired`. An ant that sees an expired lease publishes `expired` and must not run the task.

### result

```json
{
  "id": "d1a2b3c4d5",
  "node": "queenbee-1",
  "ok": true,
  "reply": "living room light is on",
  "latency": 1.42,
  "attempt": 1,
  "ts": 1790000003.0
}
```

On failure, `ok` is `false` and `reply` contains a short error string. The dispatcher accepts the **first** result per task id and ignores late or duplicate results.

## Lifecycle

```
dispatcher ── tasks/queue (attempt=N, lease_until) ──▶ ant
     ▲                                                  │
     │                                    tasks/<id>/accepted
     │                                                  │
     └──────── tasks/<id>/result (first wins) ◀─────────┘
```

1. Dispatcher publishes the task with `lease_until = now + lease` (default `timeout + 30`).
2. An ant receives it, publishes `accepted`, and starts work.
3. The dispatcher extends the deadline on acceptance.
4. If the deadline passes without a result, the dispatcher re-publishes with `attempt + 1`.
5. After `max_attempts` (default 3, maximum 5), the task is failed.
6. An ant that sees an expired lease publishes `status: "expired"` and skips the work.

## Deduplication

- Each ant keeps an LRU of the last 500 completed task ids and ignores repeats.
- Deduplication is per node, not global; the dispatcher must not re-queue a task that already has a result.
- Global deduplication and a dead-letter topic are future work.

## Errors

| Situation | Behaviour |
|---|---|
| No online ants | Dispatcher HTTP `503 {"error":"no ants online"}` |
| Max attempts reached | Dispatcher HTTP `504`, task recorded as failed |
| Malformed task JSON | Ant logs and ignores |
| Expired lease | Ant publishes `expired`; no tokens spent |
| Duplicate task id | Ant ignores |

## Broker limits (reference)

| Setting | Value |
|---|---|
| `max_connections` | 200 |
| `max_inflight_messages` | 20 |
| `max_queued_messages` | 1000 |
| `message_size_limit` | 262144 bytes |
| `persistent_client_expiration` | 7 days |
| `sys_interval` | 10 s |

These are starting points for a small swarm, not hard protocol requirements.

## ACL model

Each node may:

- write `nodes/<own-id>/#`
- write `pheromone/<own-id>`
- write `tasks/+/accepted` and `tasks/+/result`
- read `tasks/queue` (through the shared subscription)
- read `nodes/+/status` and `pheromone/+`

The controller additionally:

- writes `tasks/queue`
- reads `tasks/+/result` and `tasks/+/accepted`
- reads `$SYS/broker/#`

No node may write another node's status or the task queue.

## Versioning

- `v1` is the current draft.
- Additive fields are allowed within `v1`; consumers must ignore unknown fields.
- Removing or reinterpreting a field requires `v2`.
- A node advertises `version` in its status payload.

## Known limitations

- Payload signing and end-to-end encryption are not implemented.
- The broker is a single point of failure in the reference deployment.
- Sybil resistance relies on operator approval and per-node credentials.
- Results are not independently verified; operators should sample with golden tasks.
