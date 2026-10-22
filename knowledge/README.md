# Knowledge

SwarmNode shares **lessons**, not raw data. A lesson is a small, signed, scoped instruction that must pass the receiving device's own golden test before it can change behaviour.

This is where the colony earns its name: one operator works out how to integrate a device, and every other node can inherit that work — after verifying it locally.

## Why device recipes come first

Smart devices are the colony's first practical application. The outcome is observable, the risk can be graded, and a recipe is reusable across homes with the same model. Every new device integration makes the shared library more useful, which makes the next device easier — the first real network effect. See [../docs/applications.md](../docs/applications.md).

## Two kinds of lesson

| `kind` | Purpose | Example |
|---|---|---|
| `behaviour` (default) | An agent instruction: wording, routing, tool choice, recovery step | "When the user says 開燈 without a room, ask which room once." |
| `device_recipe` | How to integrate a smart-home device: reusable device facts, abstract steps, observable verification, risk class | "ExampleCo Plug X1, Wi-Fi, Home Assistant integration, verify state round-trip." |

Both kinds use the same envelope: signature, scope, expiry, confidence and source.

## How a device recipe spreads

1. An operator gets a device working on their own node.
2. The agent drafts a recipe: category, vendor, model, protocol, firmware band, integration, generic steps, verification and risk.
3. A **sanitizer** removes anything local: IP/MAC addresses, hostnames, SSIDs, entity IDs, room names, account names, URLs with tokens, exact schedules.
4. The operator reviews the draft and chooses to publish it. The node signs it with the device key.
5. Another node verifies the signature, trust level, expiry, scope and PII, then checks whether its own device matches (category, vendor, model, protocol, firmware, adapter).
6. It runs the recipe's `verification` locally. Only an observable pass moves the lesson to `adopted/`.

If there is no matching device or adapter, the recipe stays in `untested/`. It is never executed blind.

## Risk policy

| `risk` | Examples | Policy |
|---|---|---|
| `low` | lights, plugs, media, read-only sensors | May run automatically after the local gate passes. |
| `medium` | climate, covers, irrigation | First run needs operator confirmation; later runs may be automatic. |
| `high` | locks, doors, heaters, anything unsafe to actuate blindly | Operator confirmation on **every** actuation. A recipe can suggest, never decide. |

Risk is declared by the author, but the receiving node may raise it. It may never lower it.

## Gate

```
new lesson → pending/ → verify signature, expiry, scope, trust, PII, injection
            → behaviour: run local golden set with and without the lesson
            → device_recipe: match device + adapter, then run `verification`
            → adopted/ (not worse) or rejected/ (worse)
            → untested/ (no matching hardware) — inert
```

A lesson from an unknown author is inert. `--allow-untrusted` may be used for testing, but such a lesson is never activated.

## Privacy rules

Never include:

- names, addresses, phone numbers, identity numbers, account names;
- MAC/IP addresses, hostnames, SSIDs, device IDs, entity IDs, room names;
- URLs containing tokens, signed links or local addresses;
- exact timestamps, routines, schedules or trading parameters;
- recordings, images, documents or base64 blobs;
- code, HTML, scripts, or prompt-injection text;
- credentials of any kind.

When a step needs a secret, write "use the credential already stored in the vendor app" — never the credential itself.

## Format

See [schema/lesson.schema.json](schema/lesson.schema.json) and a sanitized example at [examples/device-recipe.example.json](examples/device-recipe.example.json). Required envelope fields:

- `cue` — when the lesson applies;
- `lesson` — a compact, testable instruction;
- `evidence` — why it is believed correct (golden test or independent reproductions);
- `scope` — domains and languages;
- `author`, `sig` — Ed25519 identity and signature;
- `created`, `expires` — validity window;
- `source` — `own`, `endorsed` or `community`;
- `version` — integer.

`device_recipe` additionally requires `device`, `risk`, `steps` and `verification`.

## Keys

Signing keys live on the device under `~/.swarmnode/keys` with mode `0600`. They are never committed. `trusted_keys.example` shows the public-key format only.

## Contributing lessons

Open a pull request adding a JSON file under `community/` with a valid signature, evidence and a sanitized body. Maintainers may endorse a lesson, but endorsement does not bypass a device's local gate, and it does not lower a risk class.
