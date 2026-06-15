---
name: journal
description: Log today's work by reconstructing it from git history across all your repos. Use at the end of a day to record what was done, optionally split by work vs personal.
argument-hint: "Optional note to add to today's entry"
---

Reconstruct today's work from **git history** (not session context) and write it to the journal. Run it at the end of the day; re-running regenerates today's entry idempotently.

## Config

Read from `~/.claude/CLAUDE.md`:

- **`journal-backend`** — `obsidian` (default) | `inkdrop`.
- **`journal-path`** — where journal files live (for `obsidian`).
- **`journal-roots`** — comma-separated directories to scan for git repos (where you keep your projects). Defaults to `~` if unset.
- **`journal-emails`** — comma-separated list of ALL your git author emails (every identity you commit with). Used to attribute commits across repos that may be configured with different `user.email`.
- **`journal-work-hosts`** — optional comma-separated remote-host substrings that mark a repo as work (e.g. your company's git host). Repos whose `origin` matches go under a **Work** section; everything else under **Personal**. If unset, no split — all repos go under one list.

If `journal-backend` is missing, ask the user once and offer to write the keys. If it names an unsupported backend, stop: `journal-backend <x> not supported; supported: obsidian, inkdrop`. If `journal-emails` is missing, fall back to each repo's `git config user.email` and warn that cross-identity commits may be missed.

## Reconstruct the day

1. Find repos: for each directory in `journal-roots`, run `find <root> -maxdepth 4 -type d -name .git` and take each parent as the repo root. Depth 4 catches both top-level repos (`.git` at depth 2) and repos nested two levels under a root. Exclude the vault repo itself (the git root containing `journal-path`) to avoid logging the journal's own commits.
2. For each repo, collect today's commits across **all branches** (not just HEAD) authored by any of your emails:
   ```bash
   git -C "$repo" log --all --since="00:00" --no-merges \
     --pretty=format:'%h%x09%ae%x09%s'
   ```
   Keep only lines whose author email (2nd field) is in `journal-emails`; drop the rest (others' commits). `--all` is essential: work is often done on per-ticket feature branches, so the checked-out branch (often `master`/`main`) may not contain today's commits. Skip repos with no matching commits.
3. If `journal-work-hosts` is set, classify each repo by its `origin` remote host:
   ```bash
   git -C "$repo" remote get-url origin
   ```
   - host matches an entry in `journal-work-hosts` → **Work**
   - otherwise → **Personal**

   If `journal-work-hosts` is unset, skip the split and list all repos under one section.
4. For each repo with commits, synthesize a 1-line summary from that day's commit subjects (derivable from the commits — no session context needed).

## Build the entry

```markdown
# YYYY-MM-DD

## Work
### <repo-name>
> <1-line summary synthesized from the day's commits>
- <commit subject> (`<short-sha>`)

## Personal
### <repo-name>
> <1-line summary synthesized from the day's commits>
- <commit subject> (`<short-sha>`)
```

- Omit a section entirely if it has no commits. With no `journal-work-hosts`, drop the section headers and list repos directly under the date.
- No `Prompt` or `Difficulties` fields — they are not derivable from git.
- If the user passed an argument, append it under a `## Notes` section.

## Write

### obsidian (default)

1. Write the entry to `<journal-path>/<YYYY-MM-DD>.md`, **overwriting** any existing file for today (the reconstruction is the full day, so regeneration is idempotent).
2. Commit it in the vault so the entries are versioned:
   ```bash
   git -C "<vault-root>" add "<journal-path>/<date>.md"
   git -C "<vault-root>" commit -m "journal: <date>" >/dev/null 2>&1 || true
   ```
   (`<vault-root>` is the git root containing `journal-path`.)

### inkdrop

Find or create today's note (`YYYY-MM-DD`) in the `Journal` notebook (`list-notebooks`). Write the reconstructed entry. Status `none`.
