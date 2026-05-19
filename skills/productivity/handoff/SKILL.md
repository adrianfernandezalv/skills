---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
---

Write a handoff document summarising the current conversation so a fresh agent can continue the work.

Do not duplicate content already captured in other artifacts (PRDs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.

Suggest the skills to be used, if any, by the next session.

## Output

Find the Inkdrop note for the current task (use `search-notes`). Append a dated entry under the `## Handoff` section (create the section if it doesn't exist):

```markdown
## Handoff

### YYYY-MM-DD
- Decided: <key decisions made this session>
- Open: <unresolved questions or pending items>
- Files/notes/ADRs: <references to relevant artifacts>
- Next: <what the next session should do>
- Skills to use: <e.g. tdd, grill-with-docs>
```

Do not replace previous handoff entries — always append. The full history must be preserved.
