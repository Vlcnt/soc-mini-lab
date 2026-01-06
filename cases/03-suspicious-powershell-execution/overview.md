# Case 03 — Suspicious PowerShell Execution

## Case summary
- **Severity (initial):** Low → Medium (context-dependent)  
- **Confidence (initial):** Low → Medium (depends on command line visibility)  
- **Status:** Open (until validated)  
- **Primary signals:** Windows Security log (Event ID 4688) + command line (if enabled)  
- **Primary risk:** Living-off-the-land execution / obfuscation / automation persistence  
- **MITRE ATT&CK (light):** Execution / PowerShell

## Goal
Handle suspicious PowerShell executions using behavioral signals:
- “living-off-the-land” parameters
- context (account, parent process, frequency)

Without over-attributing: PowerShell is often legitimate.

## Data sources
- Windows Security log (Event ID 4688)
- Live state: processes, parent process (if available)
- Logging configuration state (command line visibility)

## Visibility prerequisites
- Audit Process Creation enabled
- To see the command line in Event ID 4688:
  “Include command line in process creation events” (Group Policy)

## Signal
- Security **Event ID 4688** showing `powershell.exe` with suspicious flags such as:
  - `-NoProfile`
  - `-ExecutionPolicy`
  - `-EncodedCommand` / `-enc`
  - `-WindowStyle Hidden`

## What I can prove
- A PowerShell execution occurred
- Observable parameters (if logging is configured)
- Minimal context: timestamp, account, and sometimes parent

## What I cannot prove (with 4688 alone)
- Malicious intent vs legitimate administration
- What the payload actually does without additional sources

## Triage outcome (default)
Classify as suspicious when:
- suspicious flags + automated context (task/service) + repetition
Decision boundary is documented in `decision-boundary.md`.

## Remediation (high level)
- Validate legitimacy (owner/change) or escalate according to SOC policy
