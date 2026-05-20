---
name: journal
description: Log today's session in the project Journal. Use at the end of any work session to record what was done, how, and what's next.
argument-hint: "Task title or brief description of the session (optional)"
---

Append a session log to today's Journal note in Inkdrop.

## Find or create today's note

- **Title**: today's date in `YYYY-MM-DD` format
- **Notebook**: the `Journal` notebook of the current project. Use `list-notebooks` to locate it. For work projects use `<Company>/<project>/Journal`.
- If the note already exists: append a new `## Log:` section.
- If it doesn't exist: create it with status `none`.

## Append the log entry

```markdown
## Log: <task title>

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

If the user passed arguments, use them as the task title and context for the log.

Do not duplicate content already in commits, PRDs, or issue notes — reference them by path or URL instead.
