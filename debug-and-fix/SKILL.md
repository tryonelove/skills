---
name: debug-and-fix
description: Systematically debug a reported issue by exploring the codebase, forming hypotheses, adding targeted debug logs, validating the correct hypothesis, and implementing a fix. Use when the user reports a bug, unexpected behavior, or asks to investigate why something isn't working.
---

# Debug and Fix

## Workflow

### 1. Explore the codebase first

Before forming any hypothesis, explore the relevant code:

- Locate files related to the reported behavior (entry points, handlers, data flow)
- Read the relevant code paths end-to-end
- Check recent git changes in that area if the bug is a regression (`git log -p -- <file>`)
- Look for similar patterns elsewhere in the codebase that work correctly

Only move to hypothesis formation after you have a solid understanding of the code.

### 2. Form 2–4 hypotheses

Based on what you've read, write out explicit hypotheses that could explain the bug. For each hypothesis:

- State what you think is happening
- Identify what evidence would confirm or rule it out
- Note if it can be confirmed by code reading alone (prefer this over adding logs)

**Resolve hypotheses via code reading when possible.** Only add debug logs for hypotheses that require runtime observation.

### 3. Add targeted debug logs

For hypotheses that need runtime confirmation:

- Add minimal, targeted logs — one or two per hypothesis
- Use a consistent prefix like `[DEBUG]` for easy filtering and removal
- Log the specific value or condition that distinguishes between hypotheses
- Place logs at the decision point, not throughout the function

Example:

```ts
console.log(
  "[DEBUG] hypothesis-name: value =",
  someValue,
  "expected =",
  expectedValue,
);
```

Ask the user to run the code and share the output if you cannot run it yourself.

### 4. Identify the root cause

Once you have log output (or code-reading confirmation):

- State which hypothesis is confirmed and why
- Remove all `[DEBUG]` logs before implementing the fix
- Explain the root cause clearly in one or two sentences

### 5. Implement the fix

- Make the minimal change that addresses the root cause
- Do not refactor surrounding code unless it caused the bug
- If the fix has a non-obvious risk, briefly note it
