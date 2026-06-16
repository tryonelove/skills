# Debug Report: <short bug title>

- **Status**: TODO | IN PROGRESS | DONE
- **Risk**: LOW | MED | HIGH
- **Confidence**: LOW | MED | HIGH
- **Planned at**: commit `<short SHA>`, <YYYY-MM-DD>

<!--
Include "Planned at" only when git metadata is available.
Skip it entirely when git metadata is unavailable.
-->

## Summary

- **Symptom**:
- **Trigger**:
- **Root cause**:
- **Impact**:

## Reproduction

- **Status**: REPRODUCED | PARTIAL | NOT REPRODUCED
- **Command / path**:
- **Input / state**:
- **Expected**:
- **Actual**:

## Evidence

Concrete facts that support the diagnosis.

Use file paths whenever relevant. Add line ranges only when useful/stable. Include user-provided artifacts only as distilled excerpts.

- `<file/path>` — <relevant observation>
- User-provided error: `<smallest relevant error excerpt>`
- Runtime/test evidence: `<smallest relevant result>`

## Diagnosis

- **Confirmed hypothesis**:
- **Why this is the root cause**:
  - ...

<!--
Include "Ruled out" only when it prevents future backtracking or explains why an obvious alternative is not the cause.
Omit it otherwise.
-->

- **Ruled out**:
  - `<hypothesis>` — `<evidence that ruled it out>`

## Current state

Self-contained context the executor needs. Do not write “as discussed”.

Include:

- relevant files and their roles
- short code excerpts with `file:line` markers when useful
- repo conventions or existing patterns to follow
- relevant docs/design/API constraints, quoted or linked by path
- any important context from user-provided logs, screenshots, or repro notes

Example:

- `src/orders/api.ts` — order creation endpoint; request input is validated here.
- `src/orders/api.test.ts` — existing API test pattern to follow.
- Existing validation pattern: `src/users/api.ts` normalizes request input before schema validation.

## Commands you will need

Exact commands from this repo. Do not guess. Include only commands the executor is likely to need.

| Purpose   | Command     | Expected on success |
| --------- | ----------- | ------------------- |
| Install   | `<command>` | `<expected result>` |
| Typecheck | `<command>` | `<expected result>` |
| Test      | `<command>` | `<expected result>` |
| Lint      | `<command>` | `<expected result>` |

## Suggested executor toolkit

Optional. Include only when relevant skills/tools plausibly exist in the executor's environment. Skip this section otherwise.

- `<skill/tool>` — why it is useful for this report.

## Scope

**In scope** (the only files/areas you should modify):

- `<file-or-area>`
- `<behavior allowed to change>`

**Out of scope** (do NOT touch, even though they look related):

- `<file-or-area>` — <reason>
- `<behavior/API/product constraint>` — <reason>

## Recommended fix

- **Approach**:
- **Why this approach**:
- **Alternatives considered**:
  - `<alternative>` — <why rejected>

- **Implementation plan**:
  1. ...
  2. ...
  3. ...

## Steps

### Step 1: <imperative title>

What to do precisely. Reference exact files/symbols. Include target code shape when load-bearing.

**Verify**: `<command>` → <expected output>

### Step 2: <imperative title>

What to do precisely.

**Verify**: `<command>` → <expected output>

<!--
Each step should be small enough to verify independently.
Order steps so the codebase is not left broken between steps when possible.
-->

## Debug probes used

Optional. Include when probes/logs materially confirmed or rejected a hypothesis. Skip otherwise.

- **Probe ID**: `[DBG session=<id> probe=<id> point=<name>]`
  - **Purpose**:
  - **Location**:
  - **Observed values**:
  - **Conclusion**:
  - **Status**: Removed from code.

<!--
Include exact probe code only when the next executor must recreate the probe.
Do not paste large logs.
-->

## Done criteria

Machine-checkable. ALL must hold:

- [ ] Bug no longer reproduces using `<command or UI path>`.
- [ ] Root cause is addressed directly, not worked around elsewhere.
- [ ] Regression test added or updated for `<case>`, if the repo has a clear test pattern.
- [ ] Relevant verification command passes: `<command>`.
- [ ] No `[DBG ...]` probes or temporary debug logs remain in the code.
- [ ] No files outside the in-scope list are modified.
- [ ] Scope constraints are respected.
- [ ] Existing public behavior/API shape remains unchanged, if applicable.

## STOP conditions

Stop and report back. Do not improvise if:

- The code at the locations in "Current state" does not match the excerpts.
- A step's verification fails twice after a reasonable fix attempt.
- The fix appears to require touching an out-of-scope file.
- The confirmed root cause no longer matches the live code.
- You discover a key assumption from the diagnosis is false.
- The fix appears to require a public behavior/API change that is out of scope.

## Maintenance notes

Optional. Include only when useful.

For the human/agent who owns this code after the change lands:

- future changes that interact with this fix
- reviewer concerns
- known fragile areas
- deferred follow-up and why
