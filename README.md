# adrianfernandezalv/skills

[Claude Code](https://claude.ai/code) skills for an AI-assisted development pipeline — from idea to shipped code.

## Installation

Install all skills globally:

```bash
npx skills@latest add adrianfernandezalv/skills -y -g
```

Install a single skill:

```bash
npx skills@latest add adrianfernandezalv/skills skill=<name> -y -g
```

The installer detects which agents you have installed (Claude Code, Cursor, Codex, etc.) and creates symlinks automatically for each. The skills are written and optimized for Claude Code — behavior on other agents may vary.

## Pipeline

The skills are designed to work together end-to-end:

```
/grill-me                      → clarify your idea
/grill-with-docs               → stress-test against your codebase's domain model
/to-prd                        → turn the conversation into a structured PRD
/to-issues                     → break the PRD into independently-grabbable slices
/tdd                           → implement each slice with red-green-refactor
/improve-codebase-architecture → find deepening opportunities after implementation
/journal                       → reconstruct today's work from git history (work vs personal)
/handoff                       → write a handoff comment on the active issue
```

## Skills

### Engineering

| Skill | Description |
|---|---|
| `grill-with-docs` | Stress-test a plan against the codebase's domain model (CONTEXT.md, ADRs) and update docs inline |
| `to-prd` | Turn the current conversation into a structured PRD |
| `to-issues` | Break a PRD into independently-grabbable implementation slices |
| `tdd` | Red-green-refactor TDD loop for features and bug fixes |
| `improve-codebase-architecture` | Find refactoring opportunities and deepen module boundaries |

### Productivity

| Skill | Description |
|---|---|
| `grill-me` | Interview to reach shared understanding before starting work |
| `journal` | Reconstruct today's work from git history across your repos, split work vs personal |
| `handoff` | Write a handoff comment on the active issue for the next session |

## Integrations

Four skills (`to-prd`, `to-issues`, `journal`, `handoff`) persist state across sessions and adapt to a configurable backend. Backends dispatch independently, so combinations (e.g. GitHub issues + Inkdrop journal) work out of the box.

### Issue backend — `output:` in `CLAUDE.local.md`

Selects where `to-prd` / `to-issues` / `handoff` write. Default is `github` when the key is absent.

| `output:` | PRD | Slices | Handoff |
|---|---|---|---|
| `github` (default) | Epic tracking issue (label `epic`), PRD in the body | Native **sub-issues** of the Epic, `type:`/`status:` labels, release as **milestone** | Comment on the active issue |
| `jira` | Jira ticket description | Child Jira issues with blocking links | Comment on the ticket |
| `inkdrop` | `Spec` notebook note | `Issues` notebook notes, type + release tags | `## Handoff` section in the issue note |

With `github`, issue status is tracked by labels (no GitHub Project needed): `status: to-define` → `status: selected-for-dev` → `status: in-progress` → closed (Done); `wontfix` = dropped. Labels and milestones are created on-demand.

### Journal backend — `journal-backend` in `~/.claude/CLAUDE.md`

`/journal` reconstructs the day from git history and writes it where `journal-backend` says (`journal-path` gives the location):

| `journal-backend` | Behavior |
|---|---|
| `obsidian` (default) | Writes `<journal-path>/<date>.md` and commits it (vault is a git repo) |
| `inkdrop` | One note per day in the `Journal` notebook |

`/journal` scans `journal-roots` for repos, attributes commits via `journal-emails` (all your identities, across all branches), and optionally splits Work vs Personal by `journal-work-hosts`:

```
# ~/.claude/CLAUDE.md
journal-backend: obsidian
journal-path: ~/notes/Journal
journal-roots: ~/code                 # dirs to scan for git repos
journal-emails: you@example.com, you@work.com
journal-work-hosts: git.company.com   # optional — marks matching repos as "work"
```

## Repository conventions

Some skills expect specific files to exist in your project. Create them once and the skills will use them automatically:

| File | Used by | Purpose |
|---|---|---|
| `CONTEXT.md` | `grill-with-docs`, `improve-codebase-architecture`, `tdd` | Domain glossary — canonical terms and relationships for this codebase |
| `docs/adr/` | `grill-with-docs`, `improve-codebase-architecture` | Architectural Decision Records — decisions the skills won't re-litigate |
| `CLAUDE.local.md` | `to-prd`, `to-issues`, `handoff` | Project-level config (`output:` selects the issue backend; default `github`) |

`CONTEXT.md` and `docs/adr/` are created lazily by the skills themselves the first time they're needed — you don't have to set them up manually.

## Credits

Based on [mattpocock/skills](https://github.com/mattpocock/skills) by [Matt Pocock](https://github.com/mattpocock), MIT license.
