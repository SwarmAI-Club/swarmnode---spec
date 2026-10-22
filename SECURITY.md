# Security Policy

## Reporting

Email <support@swarmnode.space> with:

- affected component and version/commit;
- reproduction steps or a proof of concept;
- impact assessment;
- whether you intend to publish, and a proposed date.

Do not open a public issue, and do not test against nodes you do not own.

## Scope

In scope:

- broker authentication and ACL bypass;
- cross-node topic access;
- task forgery or result spoofing;
- lesson-gate bypass or signature verification flaws;
- credential leakage in installers, logs or releases;
- remote code execution in the agent or bridge.

Out of scope:

- denial of service from a node you control;
- physical access to a device;
- issues in upstream models or third-party runtimes (report upstream, and tell us if it affects SwarmNode);
- social engineering.

## Response

Best effort:

| Stage | Target |
|---|---|
| Acknowledgement | 72 hours |
| Triage and severity | 7 days |
| Fix or mitigation plan | 30 days for high severity |

We will credit reporters unless asked otherwise. Please allow 90 days before public disclosure, or coordinate a shorter window if a fix ships sooner.

## Safe harbour

We will not pursue legal action for good-faith research that follows this policy, stays within your own nodes, and avoids privacy violations and service disruption.
