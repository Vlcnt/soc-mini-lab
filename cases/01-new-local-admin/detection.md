# Detection

## Why it matters
Creating an account and adding it to a privileged group is a common pattern of:
- legitimate administrative activity
- credential abuse
- local escalation post-compromise

Detection should identify the **pattern**, then hand off to triage.

## Query (PowerShell)

### Latest account creation events (Event ID 4720)
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4720} -MaxEvents 20
```

### Latest local group addition events (Event ID 4732)
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4732} -MaxEvents 20
```

## Evidence to capture (minimum)
- Timestamp(s)
- Subject (actor) from the event details
- Target account name
- Target group (e.g., Administrators)
- Host/asset name

## Expected benign causes
- New admin account created during onboarding or migration
- Temporary elevation for troubleshooting within a change window
- Local break-glass account creation following an incident procedure

## Common false positives / pitfalls
- Missing context: a valid change exists but is not referenced in the SOC ticket
- Admin tooling/automation creating accounts (still needs authorization validation)
- Log retention gaps: you see 4732 but not the original 4720

## Escalation triggers (raise severity/priority)
- Account name deviates from naming conventions
- Action occurs outside normal admin windows
- Actor account is unexpected (non-admin user, service account, unknown SID)
- Repeated changes across multiple hosts in a short window
