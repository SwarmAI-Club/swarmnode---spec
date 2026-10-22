# Applications

The colony is infrastructure until it does something useful for the person in the room. The first application is deliberately small and testable.

## First: smart-device recipes

**One operator works out how to integrate a device; every other node can inherit that work.**

Why this comes first:

- The result is observable: the light turns on, the state round-trips, the sensor reports.
- Risk can be graded, so a lamp can be low risk while a lock is never automatic.
- Devices are standardised by vendor, model, protocol and firmware, so a recipe is reusable.
- The privacy boundary is clean: share **how to integrate a model**, never the home's layout, IDs or credentials.
- It is easy to demonstrate: one lamp, one hub, two nodes.

### The flywheel

```
more operators integrate devices
        ↓
more sanitized recipes (vendor / model / protocol / firmware)
        ↓
new nodes match and verify recipes locally
        ↓
fewer devices need to be worked out from zero
        ↓
the device feels smarter, so more operators join
```

This is the colony's first real network effect. It is not shared compute, and it is not a token.

### Status

- Schema and a sanitized example: done (`knowledge/`).
- Risk policy and unmatched-hardware behaviour: specified.
- Sanitizer, adapter registry and reproduction tracking: Phase 2.
- Pilot: KD Tuya Wi-Fi + Matter E27 bulb, expected 2026-10-25. Test over Wi-Fi/Tuya first; Matter/HomeKit needs a hub.

### What it is not

- Not remote control of someone else's home.
- Not sharing raw sensor data, camera frames, entity names or credentials.
- Not automatic actuation on unmatched hardware.
- Not a reason to lower the risk class of locks, doors or heaters.

## Second: behavioural lessons

Agent lessons (wording, routing, recovery) already use the same signed envelope and local gate. They make the device better at conversation and tool use. Device recipes make it better at the physical world.

## Later

- Scenes and routines as reusable recipes.
- Sensors, energy monitoring and anomaly lessons.
- Media and playback recipes.
- Local agent skills that combine device state with language.

Each one opens only when the previous one has a working gate. The order is a product decision, not a promise.
