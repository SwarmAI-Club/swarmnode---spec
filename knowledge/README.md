# Knowledge

SwarmNode shares **lessons**, not raw data. A lesson is a small, signed, scoped instruction that must pass the receiving device's own golden test before it can change behaviour.

## Format

See [schema/lesson.schema.json](schema/lesson.schema.json). Required fields:

- `cue` — when the lesson applies;
- `lesson` — a compact, testable instruction;
- `evidence` — why it is believed correct;
- `scope` — domains and languages;
- `author`, `sig` — Ed25519 identity and signature;
- `created`, `expires` — validity window;
- `source` — `own`, `endorsed` or `community`;
- `version` — integer.

## Gate

```
new lesson → pending/ → verify signature, expiry, scope, PII, injection
            → run local golden set with and without the lesson
            → adopted/ (not worse) or rejected/ (worse)
```

A lesson from an unknown author is inert. `--allow-untrusted` may be used for testing, but such a lesson is never activated.

## Privacy rules

Never include:

- names, addresses, phone numbers, identity numbers, MAC/IP addresses, URLs;
- exact timestamps, account or strategy parameters;
- recordings, images, documents or base64 blobs;
- code, HTML, scripts, or prompt-injection text.

## Keys

Signing keys live on the device under `~/.swarmnode/keys` with mode `0600`. They are never committed. `trusted_keys.example` shows the public-key format only.

## Contributing lessons

Open a pull request adding a JSON file under `community/` with a valid signature and evidence. Maintainers may endorse a lesson, but endorsement does not bypass a device's local gate.
