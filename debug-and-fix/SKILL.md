---
name: debug-and-fix
description: Systematically diagnose and optionally fix reported issues. Use when the user reports a bug, unexpected behavior, asks why something is failing, wants a diagnosis/report, or wants targeted debugging with runtime logs.
---

# Debug and Fix

## Modes

Default mode is `fix`.

- `report`, `diagnose`, `diagnose-only`, `investigate`, or `why`: find the root cause and report it. Do not implement a fix. Do not leave debug logs in the code.
- `fix`: find the root cause, remove all debug logs, implement the minimal fix, and verify it.

## Workflow

### 1. Reproduce or pin down the failure

- Capture the exact command, UI path, input, error text, expected behavior, and actual behavior.
- If you can run it, reproduce before reading deeply.
- If you cannot run it, ask for the smallest missing repro detail or command output.
- Prefer a minimal failing case over broad exploration.

### 2. Explore the relevant code

- Locate files related to the reported behavior (entry points, handlers, data flow)
- Read the relevant code paths end-to-end
- Check recent git changes in that area if the bug is a regression (`git log -p -- <file>`)
- Look for similar patterns elsewhere in the codebase that work correctly

Only move to hypothesis formation after you have a solid understanding of the code.

### 3. Form 2–4 hypotheses

Rank hypotheses by cheapest evidence first. For each one:

- State what you think is happening
- Identify what evidence would confirm or rule it out
- Identify what would falsify it
- Note if it can be confirmed by code reading alone (prefer this over adding logs)

**Resolve hypotheses via code reading when possible.** Only add debug logs for hypotheses that require runtime observation.

### 4. Add targeted debug probes

For hypotheses that need runtime confirmation:

- Add minimal probes: max 3 probes per round, max 2 runtime rounds before reassessing
- Use structured IDs so the user can filter and paste only relevant lines
- Log the specific value or condition that distinguishes between hypotheses
- Place logs at the decision point, not throughout the function

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

### 5. Identify the root cause

Once you have log output (or code-reading confirmation):

- State which hypothesis is confirmed and why
- Remove all `[DBG ...]` probes before reporting completion or implementing the fix
- Explain the root cause clearly in one or two sentences

If no hypothesis is confirmed after two probe rounds, stop and reassess the repro, assumptions, and code path instead of adding more logs.

### 6. Report or fix

In `report` mode, stop after the diagnosis and use this shape:

```md
## Debug Report
- Repro:
- Root cause:
- Confirmed hypothesis:
- Evidence:
- Fix recommendation:
- Risk:
- Verification:
- Files likely touched:
```

In `fix` mode:

- Make the minimal change that addresses the root cause
- Do not refactor surrounding code unless it caused the bug
- Add or update the smallest regression test when the repo has a clear test pattern
- Run the relevant verification command when available
- If the fix has a non-obvious risk, briefly note it
