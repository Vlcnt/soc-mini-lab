# Triage

## Triage objective
1) Confirm it’s PowerShell and with which parameters  
2) Determine if it is manual or automated (parent process)  
3) Assess impact and risk (frequency, account, context)  
4) Decide: close, validate, or escalate/contain

## Steps (practical and defensive)

### 1) Extract relevant events (Event ID 4688)
- filter for `powershell.exe`
- filter for suspicious flags

### 2) Minimal context to note
- Account (who executed it)
- Host (where)
- Time (normal window or anomalous)
- Parent process (if available in logs/telemetry)

Useful heuristic:
- `explorer.exe` → often manual
- `schtasks.exe`, `services.exe` → often automated

### 3) Risk framing (simple)
- **Low risk:** expected admin + expected timing + no obfuscation + low frequency  
- **Medium risk:** suspicious flags OR unusual timing OR unclear parent  
- **High risk:** obfuscation + repetition + unexpected account and/or correlated signals

## Triage output (paste-ready template)
- **Observed evidence:** Event ID 4688 entries for `powershell.exe` (timestamps).
- **Command line:** present/absent; key flags (if present).
- **Context:** account, host, time window, parent process (if known).
- **Limits:** cannot determine intent/payload from this telemetry alone.
- **Decision:** close / validate / escalate / contain.
