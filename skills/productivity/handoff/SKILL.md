---
name: handoff
description: Write a handoff on the active issue so the next session can pick it up where you left off.
argument-hint: "What will the next session focus on? (optional)"
---

Write a handoff on the active issue (the ticket you are leaving mid-way) so the next agent can continue. This is for **incomplete work** — a snapshot of where things stand.

Do not duplicate content already captured in other artifacts (PRDs, ADRs, commits, diffs). Reference them by path or URL instead.

> The end-of-day **Journal** is a separate skill (`/journal`) reconstructed from git history. Handoff does NOT write the journal — they fire at different moments (handoff = leaving a ticket; journal = end of day).

## Output backends

Selected by `output:` in `CLAUDE.local.md`:

- **`output: github`** — default when absent. Handoff is a **comment** on the active issue.
- **`output: jira`** — handoff is a **comment** on the active Jira ticket.
- **`output: inkdrop`** — handoff is appended under a `## Handoff` section in the active issue note.

If `output:` names an unsupported backend, stop: `backend <x> not supported; supported: github, jira, inkdrop`.

## Identify the active issue

Determine which issue this session was working on, in order:

1. An issue reference already in the conversation context.
2. The current branch name (e.g. `42-…` or `…-issue-42`) → issue `#42`.
3. If still ambiguous, ask the user for the issue number/URL.

## Write the handoff

Use this body. If the user passed arguments, treat them as context for what the next session should focus on and tailor the `Next` line accordingly.

```markdown
### Handoff — YYYY-MM-DD
- **Decided:** <key decisions made this session>
- **Open:** <unresolved questions or pending items>
- **Next:** <what the next session should do>
- **Skills to use:** <e.g. /tdd, /verify>
```

### github (default)

```bash
gh issue comment <issue#> --body-file <tmp>
```

Comments are append-only by nature — never edit a previous handoff comment, post a new one.

### jira

Add the handoff as a **comment** on the active ticket via the Jira MCP tools.

### inkdrop

Find the active issue note (`search-notes`). Append the handoff under `## Handoff` (create the section if missing). Never replace previous entries — always append.
