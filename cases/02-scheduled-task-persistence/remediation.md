# Remediation

## Objective
1) Stop the effect (containment)
2) Remove the mechanism (cleanup)
3) Verify (post-action)
4) Improve visibility (logging hardening)

> In production: remediation according to policy/change.

---

## Containment

### Disable the suspicious task (reversible)
```cmd
schtasks /change /tn "TaskName" /disable
```

### Stop the effect (if applicable)
- Close the process/app that is launched automatically
- Do not confuse “effect” with “root cause”

---

## Cleanup

### Delete the task
```cmd
schtasks /delete /tn "TaskName" /f
```

### Verify task is no longer present
```cmd
schtasks /query /tn "TaskName"
```

---

## If the effect persists after removing the task (ghost persistence)
Run triage on:
- Startup folder (including `.txt/.log/.ini` files that open with Notepad via association)
- `.lnk` shortcuts (target + arguments)
- Registry policy `Explorer\Run`
- Local logon scripts (GroupPolicy scripts)

These checks are in `command-library/02_persistence_tasks.md`.

---

## Visibility hardening

### Enable Task Scheduler Operational log
```cmd
wevtutil sl Microsoft-Windows-TaskScheduler/Operational /e:true
```

### (Lab) Enable auditing for Security Event ID 4698
> Requires Advanced Audit Policy: Audit Other Object Access Events

Check status:
```cmd
auditpol /get /subcategory:"Other Object Access Events"
```

Enable (lab):
```cmd
auditpol /set /subcategory:"Other Object Access Events" /success:enable /failure:enable
```

---

## Post-action checks
- Reboot / logout-login
- Confirm: no automatic effect
- Confirm: no suspicious residual tasks
- Document: before/after and decision rationale
