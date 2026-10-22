# Reference Installer Guide

This is a **guide**, not a bundle of scripts yet. Scripted installers will be published in a separate repository after the hardening phase (see [roadmap](roadmap.md)). Nothing here contains credentials.

## Target

- Windows 11 (22H2 or newer)
- WSL2 with Ubuntu 24.04
- 12 GB RAM minimum, 64 GB free disk
- Wired or stable Wi-Fi connection

## 1. Prepare Windows and WSL

```powershell
wsl --install -d Ubuntu-24.04
```

Reboot if prompted. In Ubuntu:

```bash
sudo apt update
sudo apt install -y python3 python3-venv python3-pip git curl mosquitto-clients
```

## 2. Get the runtime and model

Install the LiteRT-LM runtime from its official distribution, then download the model from its official source.

```bash
mkdir -p ~/swarmnode/models ~/swarmnode/data ~/swarmnode/logs
# Place the model at:
#   ~/swarmnode/models/gemma-4-e2b/model.litertlm
# Verify the published SHA-256 before use.
sha256sum ~/swarmnode/models/gemma-4-e2b/model.litertlm
```

Model weights are **not** part of this repository and keep their upstream licence. Do not re-upload them.

## 3. Configure the device

Create `~/swarmnode/data/agent.env` with mode `0600`:

```bash
install -m600 /dev/null ~/swarmnode/data/agent.env
```

```dotenv
SWARM_ID=swarm
NODE_ID=replace-me
TASK_MODE=agent
AGENT_URL=http://127.0.0.1:8090
LLM_URL=http://127.0.0.1:8085/v1/chat/completions
LLM_MODEL=gemma-4-e2b
```

If the node joins a swarm, add broker settings to `~/swarmnode/data/mqtt.env` (also `0600`):

```dotenv
SWARM_ID=swarm
NODE_ID=replace-me
MQTT_HOST=mqtt.example.net
MQTT_PORT=8883
MQTT_USER=replace-me
MQTT_PASS=replace-me
MQTT_CA=/home/you/swarmnode/data/mqtt-ca.pem
TASK_MODE=agent
```

Rules:

- Never commit these files.
- Use a unique broker password per node.
- Keep the CA bundle current; pin the issuer chain in production.

## 4. Telegram (optional)

1. Create a bot with BotFather and store the token in the service environment, not in the repository.
2. Set an allowlist of chat ids. An empty allowlist means the bot must refuse to answer.
3. Voice notes are transcribed locally; replies can be text or speech.

## 5. Self-hosted broker (optional)

For a self-hosted swarm, run Mosquitto with TLS and per-node credentials.

Minimum configuration:

```conf
listener 8883
allow_anonymous false
password_file /etc/mosquitto/swarm.passwd
acl_file /etc/mosquitto/swarm.acl
max_connections 200
max_inflight_messages 20
max_queued_messages 1000
message_size_limit 262144
persistent_client_expiration 7d
```

Create one account per node:

```bash
sudo mosquitto_passwd -b /etc/mosquitto/swarm.passwd <node-id> '<random-secret>'
```

Add an ACL block per node (see [protocol.md](protocol.md#acl-model)). The controller is the only account allowed to write `tasks/queue`.

## 6. Services

Install the agent, bridge and optional dispatcher as systemd units. Each unit should:

- run as an unprivileged user;
- restart on failure with a short delay;
- read secrets from a `0600` environment file;
- log to `~/swarmnode/logs/` without printing secrets.

## 7. Verify

```bash
# Local agent
curl -s http://127.0.0.1:8090/health

# Local LLM
curl -s http://127.0.0.1:8080/v1/models

# Presence (from any node with credentials)
mosquitto_sub -h "$MQTT_HOST" -p 8883 --cafile "$MQTT_CA" \
  -u "$MQTT_USER" -P "$MQTT_PASS" \
  -t "$SWARM_ID/v1/nodes/+/status" -C 1 -v

# One test task (controller only)
curl -s http://127.0.0.1:8091/task \
  -H 'Content-Type: application/json' \
  -d '{"text":"hello","count":1,"timeout":60,"lease":90,"max_attempts":2}'
```

Expected: the node shows `online: true`, a heartbeat arrives within 30 s, and the task returns a result or a clear `no ants online` error.

## 8. Hardening checklist

- [ ] `agent.env` and `mqtt.env` are `0600` and outside git
- [ ] broker has `allow_anonymous false`
- [ ] every node has a unique password
- [ ] only the controller can write the task queue
- [ ] TLS certificate is valid and auto-renewed
- [ ] model hash verified
- [ ] Telegram allowlist is non-empty
- [ ] logs do not contain tokens or message bodies
