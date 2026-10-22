# Changelog

## 0.1.3 — 2026-10-22

- Published the specification publicly at https://github.com/SwarmAI-Club/swarmnode---spec.

## 0.1.2 — 2026-10-22

- Lessons now have `kind`: `behaviour` (default) or `device_recipe`.
- Device recipes carry reusable device facts, risk class, generic steps, local verification and known limits; no identifiers or credentials.
- Added sanitized `examples/device-recipe.example.json`.
- Documented the colony flow, risk policy (low auto, medium first-run, high every-actuation) and `untested/` state for unmatched hardware.
- Roadmap Phase 1 adds sanitizer rules and a recipe example; Phase 2 adds adapter registry, verification harness and reproduction tracking.

## 0.1.1 — 2026-10-22

- Operationalised the roadmap: status table, repository plan, per-phase checklists, target dates and definition of done.
- Phase 1 now includes the knowledge and hardware repositories, ACL generator and three external installs.

## 0.1.0 — 2026-10-22

- Initial public specification.
- Architecture, trust boundaries and deployment profiles.
- Swarm protocol v1: topics, payloads, lease/retry lifecycle, ACL model.
- Reference installer guide (Windows 11 + WSL2, self-hosted broker).
- Signed lesson schema and gate description.
- Roadmap and governance.
