# Timeline

## Method
- If logs exist: Security Event ID 4698/4702/4699 → “strong” timeline
- If logs don’t exist: effect + enumeration + Operational log → “best effort” timeline

## Scenario B (logging on) — example
- **T0:** Event ID 4698 — task created (actor may be visible)
- **T1:** Task Scheduler Operational log — task registered/started (if enabled)
- **T2:** Observable effect at login/boot

## Scenario A (no logging) — example
- **T0:** Observable effect (at login/boot)
- **T1:** `schtasks` enumeration — task present, trigger “at logon”
- **T2:** (optional) Operational log after enabling — subsequent events only (not historical)

## Note
Always document:
- which channels were enabled **before**
- which were enabled **during** triage (avoid retroactive attribution)
