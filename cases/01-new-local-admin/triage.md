# Triage

## Triage objective
1) Identify the created account and the actor (Subject)  
2) Verify membership in the privileged group  
3) Assess risk using context, without assuming intent  
4) Decide: close as benign, escalate, or contain pending validation

## Practical steps (defensive)

### 1) Verify actual state: users and Administrators group
```cmd
net user
```

```cmd
net localgroup Administrators
```

### 2) Correlate with event logs
In Event ID 4720 / 4732 details, capture:
- Subject (who performed the action)
- Target account (created/added)
- Target group (Administrators or other privileged group)
- Timestamp

### 3) Context checklist (decision support)
- Is this a server or a user endpoint?
- Is there a change/ticket/approval?
- Does the account name match naming conventions?
- Does the actor (Subject) match expected admin accounts?
- Did it happen during business hours or outside the maintenance window?

## Decision guidance
- **Likely benign:** approved change exists + expected admin actor + naming convention + timing makes sense  
- **Suspicious (needs validation):** missing change context, unusual actor, unusual timing  
- **High risk:** repeated creations/additions, multiple hosts, unknown actor, evidence of other correlated signals

## Triage output (paste-ready template)
- **Summary:** New local account created and added to privileged group.
- **Observed evidence:** Event ID 4720 + Event ID 4732 (timestamps, Subject, Target, Group).
- **Live validation:** account exists (yes/no), membership present (yes/no).
- **Limits:** intent and approval cannot be proven from logs alone.
- **Decision:** close / escalate / contain.
- **Next actions:** owner validation, change verification, containment steps (if required).
