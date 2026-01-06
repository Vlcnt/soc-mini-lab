# 03 — Process Execution (Windows)

## Prerequisite
To see parameters in Event ID 4688 you need:
- Audit Process Creation (Advanced Audit Policy)
- “Include command line in process creation events” (GPO)

## Process creation (Event ID 4688) — latest events
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} -MaxEvents 20
```

## PowerShell in Event ID 4688
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} |
Where-Object { $_.Message -match "powershell.exe" } |
Select-Object -First 20
```

## Suspicious flags
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} |
Where-Object { $_.Message -match "-NoProfile|-ExecutionPolicy|-EncodedCommand|-enc|-WindowStyle" } |
Select-Object -First 20
```

## SOC rule of thumb
PowerShell is not suspicious “by default”.
Context that weighs heavily:
- account
- parent process
- repetition/frequency
- time window
