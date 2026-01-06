# Timeline

## Method: observable → correlation → live validation
Build a minimal, defensible timeline:
1) Event log evidence (what the OS recorded)
2) Correlation (same target account, same actor, same window)
3) Live validation (what is true right now)

## Timeline skeleton
- **T0:** Security Event ID 4720 — account created
- **T1:** Security Event ID 4732 — account added to privileged group (e.g., Administrators)
- **T2:** Live state check — account exists (yes/no); membership (present/removed)

## Example reconstruction (template)
- **T0:** Event ID 4720 — account `X` created (Subject: `Y`)
- **T1:** Event ID 4732 — account `X` added to `Administrators` (Subject: `Y`)
- **T2:** Current state — `X` present/absent; membership present/removed

## Note
If logs are incomplete (retention gaps), clearly document:
- which event(s) are missing
- what can be validated from current system state

