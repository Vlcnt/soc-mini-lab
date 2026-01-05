# Case 01 — New Local Admin

## Goal
Detect and triage:
1) creation of a new local user  
2) addition to a privileged group (e.g., Administrators)

SOC output: evidence, limits, decision, remediation, and post-action verification.

## Scenario (lab)
A local account is created and its group membership is modified to a privileged group.  
The key principle is **not to assume intent**. The activity is treated as potential escalation until validated.

## Data sources
- Windows **Security** log
- Live system state (local users and groups)

## Signal
- Security **EventID 4720**: user account created
- Security **EventID 4732**: member added to a security-enabled local group

## What I can prove
- The account was created
- Privileged group membership was modified
- The current system state confirms or disproves the log evidence

## What I cannot prove (from logs alone)
- Intent (mistake, authorized admin activity, or abuse)
- Change approval or business context without external sources (ticket, request, owner)

## Triage outcome
Handled as **potential privilege escalation** until validation:
- who requested the change
- why it was needed
- maintenance window
- affected asset and impact

## Remediation (lab)
- Containment and cleanup: privilege removal and post-action verification
- Documentation of decision and evidence
