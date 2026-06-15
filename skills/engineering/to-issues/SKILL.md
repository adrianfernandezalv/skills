---
name: to-issues
description: Break a plan, spec, or PRD into independently-grabbable slices. Use when user wants to convert a plan into issues, create implementation tickets, or break down work into issues.
---

# To Issues

Break a plan into independently-grabbable issues using vertical slices (tracer bullets).

## Output backends

Slices are published to one backend, selected by `output:` in `CLAUDE.local.md`:

- **`output: github`** — default when absent. Personal projects. Each slice is a **sub-issue** of the PRD's Epic tracking issue.
- **`output: jira`** — work projects. Each slice is a Jira issue, child of the parent ticket.
- **`output: inkdrop`** — each slice is a note in the project's `Issues` notebook.

If `output:` names a backend not listed here, stop: `backend <x> not supported; supported: github, jira, inkdrop`.

## Setup

1. Read `CLAUDE.local.md` to determine the backend (default `github`).
2. Find the source PRD:
   - **github**: `gh issue list --label epic --search "<feature>"` → note the Epic issue number.
   - **jira**: fetch the parent ticket.
   - **inkdrop**: `search-notes` for the Spec note.
   Read its full body.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes an issue reference, read its full body.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so. Use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

### 3. Draft vertical slices

Break the plan into **tracer bullet** issues. Each issue is a thin vertical slice that cuts through ALL integration layers end-to-end, NOT a horizontal slice of one layer.

Slices may be 'HITL' (require human interaction, e.g. an architectural decision or design review) or 'AFK' (implementable and mergeable without human interaction). Prefer AFK where possible.

<vertical-slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer (schema, API, UI, tests)
- A completed slice is demoable or verifiable on its own
- Prefer many thin slices over few thick ones
</vertical-slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice show:

- **Title**: short descriptive name
- **Type**: `feature` / `bug` / `chore` / `docs`
- **Release**: the milestone (`mvp-1` / `v1.0` / `v1.1` …)
- **HITL/AFK**: whether human interaction is required
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source has them)

Ask: Is the granularity right? Are dependencies correct? Should any slice be merged/split? Are type and release correct? Iterate until the user approves.

## Publish

Use the slice body template for every backend:

<issue-template>
## Description

A concise end-to-end description of this vertical slice. Describe the behavior, not layer-by-layer implementation. No file paths or code snippets (they go stale) — exception: a prototype snippet that encodes a decision precisely.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

None — or #<issue> (github) / ticket ref (jira) / link (inkdrop)

## Definition of done

- [ ] Tests pass
- [ ] Docs updated (if user-facing)
</issue-template>

### github (default)

Publish in **dependency order** so "Blocked by" can reference real issue numbers.

1. **Ensure labels exist** (on-demand, idempotent):
   ```bash
   gh label create "type: feature" --color 0E8A16 2>/dev/null || true
   gh label create "type: bug"     --color D73A4A 2>/dev/null || true
   gh label create "type: chore"   --color BFD4F2 2>/dev/null || true
   gh label create "type: docs"    --color 0075CA 2>/dev/null || true
   gh label create "status: to-define"         --color EDEDED 2>/dev/null || true
   gh label create "status: selected-for-dev"  --color FBCA04 2>/dev/null || true
   gh label create "status: in-progress"       --color 1D76DB 2>/dev/null || true
   gh label create "status: on-hold"           --color D93F0B 2>/dev/null || true
   ```
2. **Ensure the milestone exists** (create if missing):
   ```bash
   gh api repos/:owner/:repo/milestones --jq '.[].title' | grep -qx "<release>" \
     || gh api repos/:owner/:repo/milestones -f title="<release>" >/dev/null
   ```
3. **Create each slice** with the body template, `type:` label, `status: selected-for-dev`, and the milestone. Slices from `/to-issues` are already defined, so they start at `selected-for-dev` (not `to-define`, which is for raw idea capture):
   ```bash
   gh issue create --title "<slice>" \
     --label "type: feature" --label "status: selected-for-dev" \
     --milestone "<release>" --body-file <tmp>
   ```
4. **Attach as a sub-issue** of the Epic (native sub-issues; works on any `gh` via GraphQL — `--parent` is only in gh ≥ 2.94):
   ```bash
   PARENT_ID=$(gh issue view <epic#> --json id -q .id)
   CHILD_ID=$(gh issue view <child#> --json id -q .id)
   gh api graphql -f query='mutation($p:ID!,$c:ID!){addSubIssue(input:{issueId:$p,subIssueId:$c}){issue{number}}}' \
     -f p="$PARENT_ID" -f c="$CHILD_ID" >/dev/null
   ```
   The Epic's `## Tasks` widget and progress bar update automatically — do not edit the Epic body by hand.
5. **Blocked by** is a body mirror only (native dependencies need an org repo; personal repos don't have them).

### jira

Create a Jira issue per slice via the Jira MCP tools, in dependency order, as children of the parent ticket. Use the slice template. Then update the parent ticket description to list the child tickets.

### inkdrop

For each slice: ensure the two tags exist (`list-tags`, `create-tag` on-demand), create a note in the `Issues` notebook with status `none` and two tags (type + release), using the slice template. Then update the Spec note's `## Issues` section with links to every issue created.
