# SOC Command Library — Windows

Defensive command library used to support SOC investigations documented in this repository.

This library is not intended as standalone tooling, but as a **reference set of defensive commands**
used during triage, validation, and remediation phases of real investigation cases.

## Purpose
- Support evidence-based SOC investigations
- Validate signals observed in logs against live system state
- Reduce guesswork by following a consistent investigative flow

## How this library is used
Commands in this library are referenced directly inside case documentation under `cases/`.

Typical usage flow:
1. Observe a signal (log, alert, or observable behavior)
2. Use commands to validate the live system state
3. Correlate log evidence with real artifacts
4. Support triage and remediation decisions

## Organization
The library is organized by investigative theme:
- Identity & privileges
- Persistence mechanisms
- Process execution

Each file groups **defensive-only commands** relevant to that phase of investigation.

## Scope / safety
- Defensive-only commands (triage, validation, remediation)
- Intended for lab environments or authorized SOC contexts
- No offensive or exploitation guidance

