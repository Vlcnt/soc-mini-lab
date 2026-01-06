# Detection

## Why it matters
PowerShell is a legitimate tool but is often used for:
- execution of unauthorized scripts
- obfuscation (encoded commands)
- policy bypass (executionpolicy)

Detection should be based on:
- command line (if visible)
- context (parent process, account, time, frequency)

## Query (PowerShell)

### PowerShell executions in Event ID 4688
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} |
Where-Object { $_.Message -match "powershell.exe" } |
Select-Object -First 20
```

### Suspicious flags
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} |
Where-Object { $_.Message -match "-NoProfile|-ExecutionPolicy|-EncodedCommand|-enc|-WindowStyle" } |
Select-Object -First 20
```

## Evidence to capture (minimum)
- Timestamp
- Host
- Account (who executed)
- Full command line (if available)
- Parent process (if available in your telemetry)

## Expected benign causes
- IT scripts for configuration, inventory, or troubleshooting
- Endpoint management tools running PowerShell under the hood
- Vendor software maintenance scripts

## Common false positives / pitfalls
- Missing command line: without it, the signal is weaker (document the limitation)
- Admin running legitimate scripts with “hardening” flags like `-NoProfile`
- Tooling that wraps PowerShell (appears suspicious but is authorized)

## Escalation triggers
- `-EncodedCommand` / obfuscation + unusual account
- Hidden window + repeated execution
- Parent indicates automation (task/service) with no known change
- Correlated signals (new admin account, persistence artifacts, etc.)
