# soc-mini-lab — Mini SOC Lab (Windows)

Hands-on SOC mini-lab built as a **defensive portfolio**.  
The goal isn’t to “do attacks”, but to demonstrate how a SOC analyst reasons and documents decisions:

**signal → triage → limits → decision → remediation → verification**

## Executive summary
This repository contains three Windows-focused investigation cases. Each case is documented end-to-end with:
- detection logic and initial signals
- triage workflow and evidence collection
- explicit investigation limits (what can/can’t be proven)
- a defensible decision boundary
- remediation steps and post-action checks

The emphasis is on **evidence-based handling** and **visibility gaps**, not on offensive execution.

---

## What this repo demonstrates
- SOC mindset (investigation > random execution)
- Practical use of Windows Security logs and system logs
- Understanding of *visibility gaps* (when logs aren’t enough)
- Ability to document “what I can prove” vs “what I cannot prove”
- Documented and verifiable remediation (post-action checks)
- Clear decision boundaries (avoid over-attribution)

---

## Case index (quick navigation)

### Case 01 — New Local Admin
- Overview: [cases/01-new-local-admin/overview.md](cases/01-new-local-admin/overview.md)  
- Detection: [cases/01-new-local-admin/detection.md](cases/01-new-local-admin/detection.md)  
- Triage: [cases/01-new-local-admin/triage.md](cases/01-new-local-admin/triage.md)  
- Timeline: [cases/01-new-local-admin/timeline.md](cases/01-new-local-admin/timeline.md)  
- Remediation: [cases/01-new-local-admin/remediation.md](cases/01-new-local-admin/remediation.md)

### Case 02 — Scheduled Task Persistence (Ghost / Insider-like)
- Overview: [cases/02-scheduled-task-persistence/overview.md](cases/02-scheduled-task-persistence/overview.md)  
- Scenario A (no logging): [cases/02-scheduled-task-persistence/scenario-a_no-logging.md](cases/02-scheduled-task-persistence/scenario-a_no-logging.md)  
- Scenario B (logging on): [cases/02-scheduled-task-persistence/scenario-b_logging-on.md](cases/02-scheduled-task-persistence/scenario-b_logging-on.md)  
- Triage: [cases/02-scheduled-task-persistence/triage.md](cases/02-scheduled-task-persistence/triage.md)  
- Timeline: [cases/02-scheduled-task-persistence/timeline.md](cases/02-scheduled-task-persistence/timeline.md)  
- Remediation: [cases/02-scheduled-task-persistence/remediation.md](cases/02-scheduled-task-persistence/remediation.md)

### Case 03 — Suspicious PowerShell Execution
- Overview: [cases/03-suspicious-powershell-execution/overview.md](cases/03-suspicious-powershell-execution/overview.md)  
- Detection: [cases/03-suspicious-powershell-execution/detection.md](cases/03-suspicious-powershell-execution/detection.md)  
- Triage: [cases/03-suspicious-powershell-execution/triage.md](cases/03-suspicious-powershell-execution/triage.md)  
- Decision boundary: [cases/03-suspicious-powershell-execution/decision-boundary.md](cases/03-suspicious-powershell-execution/decision-boundary.md)

---

## Command library
Defensive-only commands used in the cases:
- [command-library/README.md](command-library/README.md)
- [01_identity_privileges.md](command-library/01_identity_privileges.md)
- [02_persistence_tasks.md](command-library/02_persistence_tasks.md)
- [03_process_execution.md](command-library/03_process_execution.md)

---

## Visibility prerequisites (important)
This repo includes “logging on/off” scenarios. To replicate correctly:

### Windows Security log: Process Creation (Event ID 4688)
- Enable **Audit Process Creation** (Advanced Audit Policy).
- To see full command line parameters, enable:
  **“Include command line in process creation events”**
  (Group Policy).

### Windows Security log: Scheduled Task creation (Event ID 4698)
- Event ID 4698 (“A scheduled task was created”) appears only if this subcategory is enabled:
  **Audit Other Object Access Events** (Advanced Audit Policy).

### Task Scheduler Operational log
- The channel `Microsoft-Windows-TaskScheduler/Operational` can be enabled for timeline and diagnostics.

> Note: Enabling logging increases volume and noise. In production, do it according to policy/change control.

---

## Tested on / assumptions
- Intended for Windows endpoints/servers with access to Event Viewer and local command execution.
- Assumes adequate log retention to observe relevant events (or explicitly documents gaps when retention/logging is missing).

---

## Scope / safety
- **Defensive-only** content: detection, triage, documentation, remediation.
- Intended for a lab environment or authorized context.
- No offensive “step-by-step” guidance.
