# Case 01 — New Local Admin

## Case summary
- **Severity (initial):** Medium  
- **Confidence (initial):** Medium  
- **Status:** Open (until validated)  
- **Primary signals:** Windows Security log (Event ID 4720, Event ID 4732)  
- **Primary risk:** Unauthorized privilege assignment / local escalation  
- **MITRE ATT&CK (light):** Privilege Escalation / Account Manipulation

## Goal
Detect and triage:
1) creation of a new local user  
2) addition to a privileged group (e.g., Administrators)  

SOC output: evidence, limits, decision, remediation, and post-action verification.

## Scenario (lab)
We observe the creation of a local account and a membership change toward a privileged group.  
The focus is: **do not assume intent**. Treat it as potential escalation until validated.

## Data sources
- Windows **Security** log
- Actual system state (local users and local group membership)

## Signal
- Security **Event ID 4720**: user account created
- Security **Event ID 4732**: member added to a security-enabled local group

## What I can prove
- That the account was created (log evidence)
- That membership was modified toward a privileged group (log evidence)
- Whether the current state (users/groups) confirms or contradicts the event logs (live validation)

## What I cannot prove (from logs alone)
- Intent (mistake, authorized activity, abuse)
- Change/approval context without external sources (ticket, request, owner)

## Triage outcome (default)
Handle as **potential privilege escalation** until validated:
- who requested it
- why it was needed
- whether it happened in an approved change window
- asset criticality and business impact

## Remediation (lab, high level)
- Containment: remove privileged membership and/or disable the account
- Cleanup (if required): delete the account
- Verification: confirm “before/after” state
- Documentation: capture evidence and decision rationale

> This case demonstrates how a SOC handles identity/privilege signals without over-attributing blame.
