# Case 02 — Scheduled Task Persistence (Ghost / Insider-like)

## Case summary
- **Severity (initial):** Medium  
- **Confidence (initial):** Low → Medium (depends on visibility)  
- **Status:** Open (until mechanism is explained/validated)  
- **Primary signals:** Task enumeration + Windows logs (Security Event ID 4698/4702/4699 when enabled)  
- **Primary risk:** Persistence via legitimate scheduling mechanisms  
- **MITRE ATT&CK (light):** Persistence / Scheduled Task/Job

## Goal
Analyze a persistence case involving Scheduled Tasks, highlighting:
- impact of **logging gaps** on visibility
- triage based on **effect → root cause**
- remediation and improved visibility

## Scenario (lab)
A repeatable behavior is observed at login/boot (e.g., an app auto-opens).  
The lab simulates an **insider-like “ghost”** situation: legitimate mechanisms, low noise, difficult attribution.

## Data sources
- Live state: `schtasks`, `Get-ScheduledTask`, startup artifacts
- Windows Security log (if configured): Event ID 4698/4702/4699
- Task Scheduler Operational log (if enabled): technical timeline

## Signal
### Scenario A — “No logging”
- The task exists and produces an effect, but **no** Event ID 4698 is available in the Security log.
- The SOC must build a timeline from:
  - observable effect
  - system enumeration (best-effort attribution)

### Scenario B — “Logging on”
- Security **Event ID 4698** (task created) is visible **if** this subcategory is enabled:
  **Audit Other Object Access Events** (Advanced Audit Policy).
- Optional:
  - Event ID 4702 (task updated)
  - Event ID 4699 (task deleted)

## What I can prove
- That an autostart mechanism exists (task or artifact)
- That it produces a repeatable effect
- That I can identify the mechanism via enumeration and/or available logs

## What I cannot prove (Scenario A)
- Who/when/how the task was created using only Event Log
- Intent without external context (change/owner/ticket)

## Triage outcome (default)
Treat as a **persistence candidate** until explained:
- build a timeline
- identify the responsible mechanism
- verify whether multiple autostarts exist (task + startup artifacts)

## Remediation (high level)
- Disable → remove → verify
- Improve visibility (enable Security auditing for 4698 + Task Scheduler Operational log)

> This case demonstrates how a SOC reasons even when logs aren’t enough.
