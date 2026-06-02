# Cairnveil Security - Security and Data Policies

This document contains security policies.

## Data Handling

Cairnveil Security collects only the telemetry required for contracted services. Common telemetry includes endpoint alerts, identity events, cloud audit logs, SIEM alerts, and incident artifacts approved by the customer.

Customers must not upload secrets, private keys, raw cardholder data, production passwords, or unrelated employee personal data into the evidence vault. If such data is discovered, Cairnveil Security will quarantine the item, notify the customer, and coordinate secure deletion.

## Evidence Vault

- Evidence vault data is encrypted at rest with AES-256.
- Evidence vault access requires multi-factor authentication.
- Administrative access is reviewed monthly.
- Standard retention is 180 days unless the contract specifies a shorter or longer period.
- Evidence export requires approval from the Customer Security Lead or a named customer approver.

## Incident Severity Levels

### Severity 1

Severity 1 means active compromise, confirmed ransomware activity, business-critical cloud credential abuse, or attacker control of privileged identity. Initial triage begins within 15 minutes for ShieldGuard Pro and Enterprise customers.

### Severity 2

Severity 2 means high-confidence compromise indicators, malware execution without confirmed spread, exposed sensitive storage, or suspicious privileged activity. Initial triage begins within 30 minutes for ShieldGuard Pro and Enterprise customers.

### Severity 3

Severity 3 means suspicious activity that requires investigation but has no confirmed compromise. Initial triage begins within 1 business day.

### Severity 4

Severity 4 means informational findings, hygiene recommendations, or low-risk detections. Review occurs during the next scheduled report cycle.

## Customer Notification

For severity 1 incidents, Cairnveil Security sends an initial customer notification within 30 minutes after triage confirms a credible incident. For severity 2 incidents, customer notification occurs within 1 hour after triage confirmation.

## Compliance Boundaries

Cairnveil Security supports evidence collection for SOC 2, ISO 27001, HIPAA, and PCI DSS. The company does not provide legal advice. Customers remain responsible for final regulatory interpretation, breach notification decisions, and auditor communications.

## AI Usage Policy

AI systems may summarize approved incident evidence, draft containment recommendations, and prepare executive summaries. AI systems must not receive customer secrets, private keys, raw credentials, or raw cardholder data. Human analysts must review AI-assisted incident summaries before customer delivery.
