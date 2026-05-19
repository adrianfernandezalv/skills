---
name: to-issues
description: Break a plan, spec, or PRD into independently-grabbable slices. Use when user wants to convert a plan into issues, create implementation tickets, or break down work into issues.
---

# To Issues

Break a plan into independently-grabbable issues using vertical slices (tracer bullets).

## Setup

Before starting:

1. Find the existing Inkdrop note for this task (use `search-notes`). The slices will be added there.
2. Check if `CLAUDE.local.md` exists at the project root. If it contains `output: jira`, this is a work project — slices will be created as Jira issues.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference or Inkdrop note, read its full body.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

### 3. Draft vertical slices

Break the plan into **tracer bullet** issues. Each issue is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices may be 'HITL' or 'AFK'. HITL slices require human interaction, such as an architectural decision or a design review. AFK slices can be implemented and merged without human interaction. Prefer AFK over HITL where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice, show:

- **Title**: short descriptive name
- **Type**: HITL / AFK
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the dependency relationships correct?
- Should any slices be merged or split further?
- Are the correct slices marked as HITL and AFK?

Iterate until the user approves the breakdown.

### 5. Publish the slices

**Personal project** (no `CLAUDE.local.md` or no `output: jira`):

Add a `## Slices` section to the Inkdrop note, in dependency order (blockers first):

```markdown
## Slices

- [ ] [AFK] Slice title — brief description
- [ ] [HITL] Slice title — brief description
- [ ] [AFK] Slice title (blocked by: slice above)
```

**Work project** (`output: jira` in `CLAUDE.local.md`):

Create a Jira issue for each approved slice using the Jira MCP tools. Use the issue body template below. Publish in dependency order so you can reference real ticket IDs in "Blocked by".

Then add a `## Slices` section to the Inkdrop note linking to the Jira tickets:

```markdown
## Slices

- [ ] [AFK] PROJ-123 — Slice title
- [ ] [HITL] PROJ-124 — Slice title
- [ ] [AFK] PROJ-125 — Slice title (blocked by: PROJ-123)
```

<issue-template>
## Parent

A reference to the parent Jira ticket (if applicable, otherwise omit).

## What to build

A concise description of this vertical slice. Describe the end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can, inline it and note it came from a prototype.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- A reference to the blocking ticket (if any), or "None - can start immediately".

</issue-template>
