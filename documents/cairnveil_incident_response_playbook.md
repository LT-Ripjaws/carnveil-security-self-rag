# Cairnveil Security - Incident Response Playbook

This playbook gives the notebook security-specific facts to retrieve and critique.

## Incident Response Phases

1. Prepare: confirm contacts, telemetry access, roles, and evidence storage.
2. Identify: validate alerts, determine affected assets, and classify severity.
3. Contain: isolate affected endpoints, disable abused identities, block indicators, and preserve evidence.
4. Eradicate: remove persistence, rotate exposed credentials, patch exploited systems, and close control gaps.
5. Recover: restore services, monitor for recurrence, and validate business operations.
6. Learn: deliver a post-incident report and update detection content.

## Ransomware Triage

Ransomware triage starts by identifying the first affected host, active encryption, suspected initial access, and backup integrity. Analysts should preserve ransom notes, suspicious binaries, EDR timelines, authentication logs, and file modification timelines. Customer teams should avoid wiping systems until evidence collection is complete unless safety or business continuity requires immediate action.

## Cloud Credential Abuse

For suspected cloud credential abuse, analysts review identity provider logs, cloud audit trails, access key creation, privilege escalation attempts, unusual regions, exposed storage, and recently modified security groups. Initial containment usually includes disabling or rotating the abused credential, revoking sessions, and reviewing recent privileged changes.

## Phishing Investigation

For phishing, analysts collect message headers, sender infrastructure, clicked URLs, attachment hashes, recipient list, identity sign-in logs, and mailbox forwarding rules. Containment may include blocking indicators, removing messages from mailboxes, resetting affected user sessions, and requiring password reset or MFA re-registration for compromised accounts.

## Post-Incident Reporting

Cairnveil Security delivers a written post-incident report within 5 business days after containment for severity 1 and severity 2 incidents. The report includes timeline, root cause hypothesis, affected assets, evidence summary, containment steps, residual risk, and prioritized remediation recommendations.

## Tabletop Exercises

Enterprise customers receive quarterly tabletop exercises. The exercise scenarios rotate across ransomware, cloud credential abuse, business email compromise, insider risk, and third-party compromise.
