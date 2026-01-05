# soc-mini-lab — Mini SOC Lab (Windows)

A hands-on SOC mini-lab built as a **defensive portfolio**.  
The goal is not “doing attacks”, but showing how a SOC analyst thinks:

**signal → triage → limits → decision → remediation**

---

## What this repo demonstrates
- SOC mindset (investigation > random execution)
- Practical use of Windows Security Logs and system logs
- Understanding of *visibility gaps* (when logs are not enough)
- Ability to document “what I can prove” vs “what I cannot prove”
- Documented and verifiable remediation (post-action checks)

---

## Included cases
- **Case 01 — New Local Admin**  
  Identity & privilege escalation (Security EventID 4720 / 4732)

- **Case 02 — Scheduled Task Persistence (Ghost / Insider-like)**  
  Persistence + visibility gaps + triage driven by effect and artifacts  
  (Security EventID 4698 when configured)

- **Case 03 — Suspicious PowerShell Execution**  
  Process execution + intent ambiguity  
  (Security EventID 4688 with command line when configured)

---

## How to read this repo
1. Go to `cases/`
2. Open each case `overview.md`
3. Follow detection → triage → timeline → remediation
4. Use `command-library/` for supporting commands (defensive-only)

---

## Visibility prerequisites (important)

This repository includes both “logging on / logging off” scenarios.  
To reproduce the cases correctly:

### Security log — Process Creation (EventID 4688)
- Enable **Audit Process Creation** (Advanced Audit Policy)
- To include parameters in the command line, enable:
  **“Include command line in process creation events”** (Group Policy)

### Security log — Scheduled Task creation (EventID 4698)
- EventID 4698 (“A scheduled task was created”) appears only if the subcategory is enabled:
  **Audit Other Object Access Events** (Advanced Audit Policy)

### Task Scheduler Operational Log
- The `Microsoft-Windows-TaskScheduler/Operational`
  channel can be enabled for timeline building and diagnostics

> Note: enabling logging increases volume and noise.  
> In production, enable/adjust logging only according to policy/change control.

---

## Scope / Safety
- **Defensive** content: detection, triage, and remediation
- Intended for lab environments or authorized contexts
- No offensive step-by-step guidance
