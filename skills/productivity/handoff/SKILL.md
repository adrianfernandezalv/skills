---
name: handoff
description: Write a handoff for the next agent to pick up this issue, and log today's session in the Journal.
argument-hint: "What will the next session focus on? (optional)"
---

Write a handoff on the active issue note so the next agent can continue, and append a session log to today's Journal note.

Do not duplicate content already captured in other artifacts (PRDs, ADRs, commits, diffs). Reference them by path or URL instead.

## Step 1 — Update the issue note

Find the active issue note in Inkdrop (use `search-notes` with the current task title, or the issue link if known). Read its full body.

Append under `## Handoff` (create the section if it doesn't exist):

```markdown
## Handoff

### YYYY-MM-DD
- Decided: <key decisions made this session>
- Open: <unresolved questions or pending items>
- Files/notes/ADRs: <references to relevant artifacts>
- Next: <what the next session should do>
- Skills to use: <e.g. tdd, grill-with-docs>
```

Do not replace previous handoff entries — always append.

If the user passed arguments, treat them as context for what the next session will focus on and tailor the Handoff entry accordingly.

## Step 2 — Write the Journal log

Find or create today's Journal note following the Journal rules:

- **Note title**: `YYYY-MM-DD` (today's date)
- **Notebook**: the `Journal` notebook of the current project (use `list-notebooks` to locate it; for work projects use `<Company>/<project>/Journal`)
- If the note exists: append a new `## Log:` section. If it doesn't: create it.

Append:

```markdown
## Log: <current task title>

- **Prompt**: <prompt received this session>
- **Issue**: [<issue title>](inkdrop://note/<noteId>)  ← or Jira URL for work projects

### What was done
<brief description>

### How
<brief description>

### Difficulties
<any problems encountered>

### Next
- [<next issue title>](inkdrop://note/<noteId>)
```
