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
/journal                       → log the session (what was done, how, what's next)
/handoff                       → write a handoff on the active issue for the next agent
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
| `journal` | Log today's session in the project Journal (what was done, how, what's next) |
| `handoff` | Write a handoff on the active issue for the next agent, and log the session |

## Integrations

Four skills (`to-prd`, `to-issues`, `journal`, `handoff`) persist state across sessions and adapt to whether you're working on a personal or work project.

### Default (personal projects)

Progress is saved to [Inkdrop](https://www.inkdrop.app/) using a four-notebook structure per project:

| Notebook | Contents |
|---|---|
| `Spec` | PRDs and architecture decision docs |
| `Issues` | One note per issue — tagged by type (`feature`, `bug`, `chore`, `docs`) and release (`mvp-1`, `v1.0`, …) |
| `Journal` | One note per day with a `## Log:` entry per session |
| `Ideas` | Brainstorming without a release assigned |

PRD notes and issue notes link to each other bidirectionally. This gives you a running record of decisions, slices, and session logs that survives context resets.

### Work projects (Jira)

Add a `CLAUDE.local.md` file at your project root containing:

```
output: jira
```

With this set, PRDs go into the Jira ticket description, slices become individual Jira issues with blocking relationships, and only the Journal is written to Inkdrop.

## Repository conventions

Some skills expect specific files to exist in your project. Create them once and the skills will use them automatically:

| File | Used by | Purpose |
|---|---|---|
| `CONTEXT.md` | `grill-with-docs`, `improve-codebase-architecture`, `tdd` | Domain glossary — canonical terms and relationships for this codebase |
| `docs/adr/` | `grill-with-docs`, `improve-codebase-architecture` | Architectural Decision Records — decisions the skills won't re-litigate |
| `CLAUDE.local.md` | `to-prd`, `to-issues`, `journal`, `handoff` | Project-level config (e.g. `output: jira` to enable Jira mode) |

`CONTEXT.md` and `docs/adr/` are created lazily by the skills themselves the first time they're needed — you don't have to set them up manually.

## Credits

Based on [mattpocock/skills](https://github.com/mattpocock/skills) by [Matt Pocock](https://github.com/mattpocock), MIT license.
