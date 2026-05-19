# adrianfernandezalv/skills

Personal [Claude Code](https://claude.ai/code) skills repository. Fork and adaptation of [mattpocock/skills](https://github.com/mattpocock/skills) (MIT).

## Installation

Install all skills globally:

```bash
npx skills@latest add adrianfernandezalv/skills -y -g
```

Install a specific skill:

```bash
npx skills@latest add adrianfernandezalv/skills skill=<name> -y -g
```

## Pipeline

These skills work together as an AI dev pipeline:

```
/grill-me            → clarify your idea
/grill-with-docs     → stress-test against your codebase's domain model
/to-prd              → turn the conversation into a structured PRD
/to-issues           → break the PRD into independently-grabbable slices
/tdd                 → implement each slice with red-green-refactor
/improve-codebase-architecture → find deepening opportunities after implementation
/handoff             → compact the session for the next agent
```

## Skills

### Engineering

| Skill | Description |
|---|---|
| `grill-with-docs` | Stress-test a plan against the codebase's domain model (CONTEXT.md, ADRs) and update docs inline |
| `to-prd` | Turn the current conversation into a structured PRD (customized: saves to Inkdrop) |
| `to-issues` | Break a PRD into independently-grabbable implementation slices (customized: Jira-aware) |
| `tdd` | Red-green-refactor TDD loop for features and bug fixes |
| `improve-codebase-architecture` | Find refactoring opportunities and deepen module boundaries |

### Productivity

| Skill | Description |
|---|---|
| `grill-me` | Interview to reach shared understanding before starting work |
| `handoff` | Compact a session into a handoff document for another agent (customized: saves to Inkdrop) |

> Skills marked "customized" extend the originals with Inkdrop note persistence and optional Jira integration via `CLAUDE.local.md` detection.

## Credits

Based on [mattpocock/skills](https://github.com/mattpocock/skills) by [Matt Pocock](https://github.com/mattpocock), MIT license.
