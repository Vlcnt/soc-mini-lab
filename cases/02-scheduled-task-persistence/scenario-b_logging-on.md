# Scenario B — Logging on (Security Event ID 4698 visible)

## Initial condition (lab)
- Advanced Audit Policy configured to log scheduled task creation:
  **Audit Other Object Access Events**
- The Security log contains Event ID 4698 (and potentially 4702/4699)

## SOC objective
1) Detect task creation via Security log (Event ID 4698)  
2) Build a timeline with stronger “who/when” evidence than Scenario A  
3) Correlate with the observed effect and with Task Scheduler Operational log if useful

## Query (PowerShell)

### Task creation (Event ID 4698)
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4698} -MaxEvents 20
```

### Task modification (Event ID 4702)
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4702} -MaxEvents 20
```

### Task deletion (Event ID 4699)
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4699} -MaxEvents 20
```

## SOC note
Even in Scenario B:
- logs improve attribution and timeline
- intent still requires external context (change, owner, ticket)
