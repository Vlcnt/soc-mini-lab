# Triage

## Triage objective
1) Identify the mechanism responsible for the effect
2) Understand trigger, account, actions, and risk
3) Separate “strong evidence” (logs) from “pragmatic evidence” (live state)
4) Decide whether to contain immediately or analyze first (based on risk)

## Practical steps

### 1) Capture the effect (what happens)
- What starts? (process/app/document)
- When does it start? (logon/boot/repeat schedule)
- How often?
- Which user context?

### 2) Quick task enumeration
```cmd
schtasks /query
```

### 3) Deep task enumeration (actions/triggers/run-as)
```cmd
schtasks /query /fo LIST /v
```

### 4) “Ghost/insider” triage: don’t trust names
A task can:
- run `cmd.exe` or `powershell.exe` and then launch the app
- open a file (e.g., `.txt`) that opens with Notepad via association
- launch a shortcut (`.lnk`) with non-obvious target/args

So:
- correlate **effect** with **artifact**
- also check Startup folder and policy (see command library)

### 5) Task Scheduler Operational log (technical timeline)
```powershell
Get-WinEvent -LogName "Microsoft-Windows-TaskScheduler/Operational" -MaxEvents 50
```

## Expected benign causes
- IT automation tasks (inventory, updates, monitoring)
- Software updaters and vendor maintenance tasks
- Admin-created tasks during troubleshooting

## Escalation triggers
- Task runs as SYSTEM or a privileged account without a known business reason
- Action uses `powershell.exe` / `cmd.exe` with unusual arguments
- Task name/path is intentionally misleading (masquerading)
- Persistence across reboots + no documented owner

## Triage output (paste-ready template)
- **Effect observed:** what/when/how often.
- **Mechanism identified:** task name/path + trigger + run-as + action.
- **Log visibility:** Security 4698 present/absent; Operational log enabled/disabled.
- **Limits:** attribution not possible if logs absent.
- **Decision:** contain now / validate first / escalate.
