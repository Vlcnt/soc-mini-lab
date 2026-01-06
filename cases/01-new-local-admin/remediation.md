# Remediation

## Objective
Reduce risk and restore the expected state, while maintaining traceability.

> In production: perform remediation according to policy (approval/change).

## Containment (lab)

### Remove the user from Administrators
```cmd
net localgroup Administrators <USERNAME> /delete
```

### Disable the account
```cmd
net user <USERNAME> /active:no
```

## Cleanup (lab)

### Delete the account (if required by the scenario)
```cmd
net user <USERNAME> /delete
```

## Post-action verification
```cmd
net user
```

```cmd
net localgroup Administrators
```

## Documentation checklist (SOC)
- Before/after snapshots (group membership + user status)
- Commands executed + timestamps
- Outcome (contained/cleaned/closed/escalated)
- Any external validation references (ticket/change/owner)

