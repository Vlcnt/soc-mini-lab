# Detection

## Why it matters
Creating a local account and adding it to a privileged group is a common pattern for:
- legitimate administrative activity  
- credential abuse  
- local privilege escalation after compromise  

A SOC must detect this pattern before attributing intent.

## Log signals

### Local user creation (EventID 4720)
Security EventID 4720 indicates that a new local user account was created.

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4720} -MaxEvents 20
```
### Added to Administrators group (EventID 4732)
Security EventID 4732 indicates that a user was added to a security-enabled local group, such as Administrators.

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4732} -MaxEvents 20
```
## Operational notes
- These events represent signals, not conclusions.
- The presence of these events alone does not prove malicious intent.
- Proper triage requires validation against the live system state and organizational context.


