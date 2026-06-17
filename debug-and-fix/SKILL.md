---
name: debug-and-fix
description: Diagnose and optionally fix frontend JavaScript, TypeScript, and React bugs. Use when the user reports broken UI behavior, browser/runtime errors, React rendering or hook issues, hydration problems, failing frontend tests/builds, CSS interaction bugs, asks for a debug report, wants 2-4 hypotheses with file references, needs temporary console probes, or explicitly asks for a minimal verified frontend bug fix.
---

# Debug and Fix

Diagnose frontend issues by reading the code first, then using the smallest runtime evidence needed. Work in one of two modes: `report` for diagnosis and collaboration, or `fix` for minimal verified code changes.

## Mode Selection

- Use `report` mode when the user asks why something is failing, asks to investigate/debug/diagnose/report, or describes a bug without explicitly asking for code changes.
- Use `fix` mode only when the user explicitly asks to fix, patch, repair, implement, or make code changes.
- If intent is ambiguous between diagnosis-only and editing, ask one concise clarification before changing non-debug code.
- In `report` mode, do not implement the fix. Temporary debug probes are allowed only to identify the issue and must be removed before claiming completion.
- In `fix` mode, remove all temporary probes/logs, implement the smallest confirmed fix, and run relevant verification.

## Core Workflow

1. Capture the failure shape: exact UI path/action, expected behavior, actual behavior, error text, browser/environment, input/state, and whether it reproduces.
2. Inspect code before asking the user to run anything when the code can answer the question. Trace the route/component, props, state, hooks, effects, data loaders, query/cache keys, event handlers, forms, async boundaries, styling branches, and nearby tests/stories.
3. Answer from code when possible. If code proves the root cause, report it or fix it without adding probes.
4. If the cause is not trivial, provide 2-4 working hypotheses with brief arguments and file references before adding runtime probes.
5. Confirm or reject hypotheses with the cheapest evidence: existing tests, type/lint/build output, local repro, browser checks, then targeted sanitized console probes.
6. Stop when the root cause is confirmed enough to recommend a concrete fix.

## Hypotheses

When the issue is not immediately obvious, write:

```md
Working hypotheses:
1. H1: <possible cause> -- likely because <evidence>. Refs: `<file>`, `<symbol>`.
   Confirm/rule out: <specific check>.
2. H2: ...
```

- Rank hypotheses by cheapest confirmation path.
- Include 2-4 hypotheses, not a broad brainstorm.
- Use file and symbol references; line references are useful when stable.
- Mark a hypothesis confirmed only after code, runtime, or test evidence supports it.

## Debug Probes

Use probes only when code reading cannot distinguish the hypotheses.

- Add at most 3 probes per round and at most 2 probe rounds before reassessing.
- Place probes at decision points: handler entry, branch selection, async response shape, render condition, state transition, or effect trigger.
- Log only sanitized discriminators: booleans, enum names, counts, lengths, route names, and redacted identifiers.
- Never log or ask the user to paste secrets, tokens, cookies, auth headers, passwords, full request bodies, environment variables, or PII.
- Use searchable IDs:

```ts
console.log("[DBG frontend issue=<slug> probe=H2 point=<point>]", {
  hasUserId: Boolean(userId),
  itemCount: items.length,
  activeTab,
});
```

When asking the user to run with probes, give a strict paste contract:

```md
Run `<command or UI action>`.
Paste only lines containing `[DBG frontend issue=<slug>`.
Do not paste tokens, cookies, auth headers, passwords, full request bodies, environment variables, or PII.
```

If probes remain while waiting for user output, state the exact files changed and that the work is not complete. Remove probes before the final report or fix whenever the session can continue.

## Report Mode

Goal: identify the issue quickly with the user, using code first and targeted probes only when needed.

1. State reproduction status: `REPRODUCED`, `PARTIAL`, or `NOT REPRODUCED`.
2. If code answers the question, give a concise diagnosis with file references and a recommended fix.
3. If not, give 2-4 hypotheses and either run a local check or add targeted probes with clear run/paste instructions.
4. Once the root cause is confirmed, produce a report using `references/debug-report-template.md`.
5. Remove placeholder-only sections. Include only evidence that mattered.

## Fix Mode

1. Confirm or derive the root cause before changing behavior.
2. Make the smallest scoped edit that directly fixes the confirmed cause.
3. Do not refactor adjacent code or add fallback logic, backward compatibility layers, adapters, feature flags, or legacy support unless explicitly requested.
4. Follow existing frontend patterns for state management, routing, query/cache usage, forms, styling, testing, and file organization.
5. Add or update the smallest regression test when the repo has a clear pattern and the bug is testable.
6. Run relevant verification discovered from repo scripts, docs, or CI. Do not invent commands.
7. Remove all temporary debug probes/logs.
8. Final response: root cause, changed files, verification, and residual risk if any.

## Frontend Checks

Prefer investigating these areas when relevant:

- UI events: handler binding, default prevention, disabled states, overlays, pointer events, stale closures.
- State and render: controlled inputs, derived state, memoization, keys, conditional guards, React StrictMode double invocation.
- Async and data: loading/error states, cache key mismatches, races, cancellation, stale query data, response shape, optimistic updates.
- Routing and hydration: route/search params, server/client boundaries, browser-only APIs during SSR, hydration mismatches.
- Styling and layout: overflow clipping, z-index, media/container queries, focus states, keyboard interaction.
- Verification: existing unit, component, E2E, Storybook, typecheck, lint, or build commands found in the repo.

## Output Rules

- In `report` mode, use `references/debug-report-template.md` for final reports.
- In `fix` mode, do not use the full report template unless the user asks for it.
- Keep final output short and evidence-based. Avoid unresolved open-ended TODOs unless you are explicitly waiting for user-provided probe output.
