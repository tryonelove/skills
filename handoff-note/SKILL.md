---
name: handoff-note
description: Summarize active work into a concise continuation handoff. Use when the user asks to summarize progress, create a handoff note, compress context, prepare for another agent/thread/session, remove failed paths, or keep only what is needed to continue.
---

# Handoff Note

## Output

Produce a tight handoff note that lets another agent continue the work without rereading the full thread.

Include only:

- Current goal or request
- Files, modules, commands, branches, or artifacts that matter
- Decisions already made
- Changes completed
- Current state of verification
- Remaining work, ordered by next action
- Blockers or open questions only if they affect continuation

## Rules

- Remove repetitions, abandoned hypotheses, failed paths, and incidental exploration.
- Preserve exact paths, identifiers, command names, error messages, and decisions when useful.
- Prefer bullets over prose.
- Mark uncertainty explicitly instead of inventing continuity.
- Do not include praise, narrative recap, or irrelevant chronology.
- Keep it short enough to paste into a new thread.

## Shape

Use this structure unless the user requests another format:

```markdown
Goal:
- ...

Relevant context:
- ...

Done:
- ...

Verified:
- ...

Next:
- ...

Open:
- ...
```

Omit empty sections.
