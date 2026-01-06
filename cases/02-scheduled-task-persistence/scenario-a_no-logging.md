# Scenario A — No logging (Visibility gap)

## Initial condition (lab)
- The behavior “repeats at login/boot” (observable effect)
- No Event ID 4698 events are present in the Security log (audit not enabled or no history)

## SOC objective
1) Confirm the effect (repeatable, not random)
2) Identify the mechanism (task / startup artifact / policy)
3) Document limits: “I cannot prove creation and author from logs”

## Workflow (effect → mechanism)

### 1) Confirm the effect
- Observe what starts, when (login/boot), and how often

### 2) Enumerate Scheduled Tasks
```cmd
schtasks /query
```

```cmd
schtasks /query /fo LIST /v
```

Look for:
- trigger: at logon / at startup / repeat
- run as: SYSTEM or another account
- actions: cmd / powershell / non-standard path

### 3) Correlate with Task Scheduler Operational (if you can enable it)
```cmd
wevtutil sl Microsoft-Windows-TaskScheduler/Operational /e:true
```

```powershell
Get-WinEvent -LogName "Microsoft-Windows-TaskScheduler/Operational" -MaxEvents 50
```

### 4) If the effect persists even after a “partial fix”
If you remove a task but the effect remains, consider:
- Startup folder (files/shortcuts)
- Policies Explorer\Run
- Local logon scripts (GroupPolicy scripts)

> In a “ghost/insider” scenario, the effect may be maintained by legitimate but non-obvious artifacts.
