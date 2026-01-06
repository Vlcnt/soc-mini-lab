# Decision Boundary (PowerShell)

## Principle
PowerShell is not suspicious “by default”.  
A defensible decision is based on **signals + context + risk**, and explicitly documents uncertainty.

## Classification (simple and defensible)

### 1) Likely benign
- Known admin account
- Timing consistent with change window
- Parent `explorer.exe` or expected IT tool
- No obfuscation flags
- Low/expected frequency

Outcome:
- document and close

### 2) Suspicious (needs validation)
- Suspicious flags (enc, hidden, executionpolicy)
- Unexpected account or unclear ownership
- Anomalous timing
- Automated parent (task/service) without known change

Outcome:
- deeper triage / owner validation request / escalation per SOC policy

### 3) High risk
- Frequent repetition + obfuscation + unexpected account
- Correlated signals (new privileged account, persistence artifacts, lateral movement signals, etc.)

Outcome:
- containment per SOC policy
- immediate escalation

## Guardrails
- Do not analyze payload content if unauthorized or out of scope.
- If command line is missing (no visibility), explicitly downgrade confidence and document the gap.
