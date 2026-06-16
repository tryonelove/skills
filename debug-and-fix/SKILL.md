---
name: debug-and-fix
description: Systematically diagnose and optionally fix reported issues. Use when the user reports a bug, unexpected behavior, asks why something is failing, wants a diagnosis/report, or wants targeted debugging with runtime logs.
---

# Debug and Fix

Systematically diagnose a reported issue, identify the root cause, and either produce an execution-ready debug report or apply the minimal verified fix.

## Modes

Default mode is `fix`.

- `report`, `diagnose`, `diagnose-only`, `investigate`, or `why`: find the root cause and produce a debug report. Do not implement the fix. Do not leave debug probes/logs in the code.
- `fix`: find the root cause, remove all debug probes/logs, implement the minimal fix, and verify it.

The report mode output must use `references/debug-report-template.md`.

## Core principles

- Prefer evidence over guessing.
- Prefer code reading over runtime probes when code reading can prove or disprove a hypothesis.
- Prefer a minimal failing case over broad exploration.
- Prefer the smallest fix that directly addresses the confirmed root cause.
- Do not refactor surrounding code unless the refactor is required to fix the bug.
- Do not produce a final report with unresolved open questions. Resolve them first by exploring the codebase. If the codebase cannot answer them, ask the user during the debugging session.
- If uncertainty still remains after reasonable investigation, reflect it through `Confidence` and `Risk`, not through an open-ended TODO section.

## Workflow

### 1. Reproduce or pin down the failure

Capture the exact failure shape:

- command, UI path, or action
- input/state
- error text
- expected behavior
- actual behavior
- environment details when relevant

If you can run the issue, reproduce before reading deeply.

If you cannot run it, ask for the smallest missing repro detail or command output.

Classify reproduction status using only:

- `REPRODUCED`: the failure was reproduced directly.
- `PARTIAL`: related behavior was reproduced, but not the full original scenario.
- `NOT REPRODUCED`: the diagnosis is based on code reading, logs, tests, or user-provided evidence without reproducing the original failure.

### 2. Explore the relevant code

Locate and read the relevant code paths end-to-end:

- entry points
- handlers/controllers/routes
- data flow
- state updates
- external boundaries
- tests around the failing behavior
- similar working patterns elsewhere in the repo

If the bug appears to be a regression, check recent changes around the relevant files:

```sh
git log -p -- <file>
```

Only move to hypothesis formation after you understand the relevant path.

### 3. Form 2–4 hypotheses

Rank hypotheses by cheapest evidence first.

For each hypothesis, identify:

- what might be happening
- what evidence would confirm it
- what evidence would rule it out
- whether it can be confirmed by code reading alone

Resolve hypotheses through code reading when possible.

Only add runtime probes for hypotheses that require runtime observation.

### 4. Add targeted debug probes when needed

Use runtime probes only when code reading is insufficient.

Probe limits:

- max 3 probes per round
- max 2 runtime rounds before reassessing
- place probes at decision points, not throughout the function
- log only values that distinguish between hypotheses
- use structured IDs so the user can filter and paste only relevant lines

Example:

```ts
console.log(
  "[DBG session=2026-06-15 probe=H2 point=auth-branch]",
  JSON.stringify({ userId, hasToken, route }),
);
```

When asking the user to run code, give a strict log contract:

```md
Run:
`<command>`

Paste only lines matching:
`[DBG session=<id>`

Expected probes:

- H1: confirms/rules out <condition>
- H2: confirms/rules out <condition>
```

All `[DBG ...]` probes and temporary logs must be removed before reporting completion or implementing the fix.

### 5. Identify the root cause

Once code reading, tests, logs, or probes confirm the diagnosis:

- state the confirmed hypothesis
- explain why it is the root cause
- distinguish symptom, trigger, root cause, and impact
- include rejected hypotheses only when they prevent future backtracking or explain why an obvious alternative is not the cause

Do not proceed with a vague diagnosis.

If no hypothesis is confirmed after two probe rounds, stop adding logs and reassess:

- repro accuracy
- assumptions
- code path
- environment
- whether the user needs to provide one missing fact

### 6. Produce report or apply fix

In `report` mode:

- produce a debug report using `references/debug-report-template.md`
- do not implement the fix
- do not leave debug probes/logs in the code
- make the report self-contained enough for another executor to apply the fix without seeing the conversation

In `fix` mode:

- apply the minimal scoped fix
- add or update the smallest regression test when the repo has a clear test pattern
- run the relevant verification command
- remove all `[DBG ...]` probes and temporary debug logs
- report what changed, how it was verified, and any risk

## Debug report requirements

When producing a report, use the template in `references/debug-report-template.md`.

The report is a reusable issue artifact and pre-fix checkpoint. It should be dense with necessary information but not noisy.

The report must include:

- bug-specific title
- `Status`: `TODO | IN PROGRESS | DONE`
- `Risk`: `LOW | MED | HIGH`
- `Confidence`: `LOW | MED | HIGH`
- `Planned at`: only when git metadata is available
- `Summary`
- `Reproduction`
- `Evidence`
- `Diagnosis`
- `Current state`
- `Commands you will need`
- `Scope`
- `Recommended fix`
- `Steps`
- `Done criteria`
- `STOP conditions`

Optional sections:

- `Suggested executor toolkit`: include only when relevant tools/skills plausibly exist in the executor's environment. Skip otherwise.
- `Debug probes used`: include when probes/logs materially confirmed or rejected a hypothesis.
- `Maintenance notes`: include only when the bug reveals future maintenance risk, fragile ownership boundaries, duplicated logic, reviewer concerns, or deferred follow-up.

Do not include an `Open Questions` section.

If there are unresolved questions:

1. First try to answer them by exploring the codebase.
2. If the codebase cannot answer them, ask the user during the session.
3. Only produce the report after the uncertainty is resolved enough to recommend a fix.

## Report field rules

### Status

Allowed values:

- `TODO`: diagnosis/report is complete; fix has not started.
- `IN PROGRESS`: executor has started applying the fix.
- `DONE`: fix has been applied and verified.

### Risk

Allowed values:

- `LOW`: narrow/local fix, clear root cause, low blast radius, straightforward verification.
- `MED`: touches shared logic, unclear edge cases, migration risk, or partial verification.
- `HIGH`: broad architectural change, public API/data model change, security/auth/payment path, weak confidence, or high blast radius.

### Confidence

Allowed values:

- `HIGH`: reproduced, confirmed by code/logs/tests, or directly proven from the code path.
- `MED`: strongly supported, but one important verification step was not available.
- `LOW`: plausible but missing key evidence. Avoid final reports at this level unless the user explicitly asks for the best available diagnosis.

### Planned at

Include only when git metadata is available.

Format:

```md
- **Planned at**: commit `<short SHA>`, <YYYY-MM-DD>
```

Skip the field entirely when git metadata is unavailable.

### Evidence

Evidence must be concrete.

Good evidence:

- file paths
- useful line ranges
- function/component names
- commands and results
- distilled user-provided errors/logs/repro notes
- runtime probe results
- test results

Rules:

- Use file paths whenever relevant.
- Add line ranges only when useful and likely to stay meaningful.
- Prefer function/component names over fragile single-line references when the code is likely to move.
- Include user-provided artifacts only as distilled excerpts.
- Do not paste huge logs or screenshots into the report.
- Avoid vague evidence like “looks wrong.”

### Scope

Scope is a permission boundary for the executor.

Include both:

- files/areas the executor may modify
- behavioral/product/API constraints the executor must not violate

Prefer exact files when known. Use areas when exact files are not yet known.

### Commands

Include only commands the next executor is likely to need.

Each command must include:

- purpose
- exact command
- expected result

Do not list every command attempted during investigation unless it provides useful context.

### Done criteria

Done criteria must be machine-checkable.

Avoid vague items like:

- “code is better”
- “logic is cleaner”
- “works correctly”

Prefer observable checks:

- command exits 0
- test exists and passes
- no debug probes remain
- no files outside scope changed
- public behavior/API shape unchanged

### STOP conditions

STOP conditions must prevent improvisation when the plan no longer matches reality.

Include conditions such as:

- current code does not match the report’s excerpts
- verification fails repeatedly
- fix requires touching out-of-scope files
- confirmed root cause is false
- public behavior/API change appears necessary but is out of scope
