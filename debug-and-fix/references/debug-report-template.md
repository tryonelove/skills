# Frontend Debug Report: <short bug title>

- **Status**: INVESTIGATING | DIAGNOSED | READY TO FIX | FIXED
- **Mode**: REPORT | FIX
- **Reproduction**: REPRODUCED | PARTIAL | NOT REPRODUCED
- **Risk**: LOW | MED | HIGH
- **Confidence**: LOW | MED | HIGH

## Summary

- **Symptom**:
- **Trigger**:
- **Current best explanation**:
- **Impact**:

## Evidence

Include concrete facts only. Prefer file/symbol references over broad summaries.

- **User-provided evidence**:
- **Code evidence**:
- **Runtime/test evidence**:

## Working Hypotheses

Include this section only while the root cause is not fully confirmed.

| ID | Hypothesis | Why plausible | File refs | Confirm/rule out |
| -- | ---------- | ------------- | --------- | ---------------- |
| H1 |  |  |  |  |
| H2 |  |  |  |  |

## Diagnosis

Include this section once the root cause is confirmed.

- **Confirmed cause**:
- **Why this is the root cause, not only a symptom**:
- **Rejected alternatives**:

## Recommended Fix

- **Scope**:
- **Approach**:
- **Files to touch**:
- **Tests/checks**:

## Debug Probes

Include only if probes were added or proposed.

- **Probe status**: NOT ADDED | ACTIVE TEMPORARY | REMOVED
- **Probe points and purpose**:
- **User run/paste contract**:
- **Observed sanitized values**:

## Commands

List only commands discovered in this repo. Do not guess.

| Purpose | Command | Result or expected result |
| ------- | ------- | ------------------------- |

## Done Criteria

- [ ] The issue no longer reproduces using the stated UI path or command.
- [ ] The confirmed root cause is addressed directly.
- [ ] Relevant regression coverage is added or updated when the repo has a clear test pattern.
- [ ] Relevant verification command passes.
- [ ] No `[DBG frontend ...]` probes or temporary debug logs remain.
- [ ] No files outside the stated scope changed.

## Stop Conditions

Stop and report back if:

- The live code no longer matches the cited files/symbols.
- A check fails twice after a reasonable fix attempt.
- The fix requires touching files outside the stated scope.
- A confirmed hypothesis is disproved by new evidence.
- The fix would require a product/API behavior change that was not requested.
