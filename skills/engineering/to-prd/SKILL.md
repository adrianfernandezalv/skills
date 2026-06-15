---
name: to-prd
description: Turn the current conversation context into a PRD and save it. Use when user wants to create a PRD from the current context.
---

This skill takes the current conversation context and codebase understanding and produces a PRD. Do NOT interview the user — just synthesize what you already know.

## Output backends

The PRD is written to one backend, selected by `output:` in the project's `CLAUDE.local.md`:

- **`output: github`** — default when the key is absent. Personal projects. The PRD becomes a **GitHub tracking issue** (the "Epic"), labelled `epic`, with the PRD as the issue body.
- **`output: jira`** — work projects. The PRD becomes the **description** of a Jira ticket.
- **`output: inkdrop`** — the PRD becomes a note in the project's `Spec` notebook.

If `output:` names a backend not listed here, stop and tell the user: `backend <x> not supported; supported: github, jira, inkdrop`.

## Setup

1. Read `CLAUDE.local.md` at the project root to determine the backend (default `github`).
2. Look for an existing PRD to update instead of duplicating:
   - **github**: `gh issue list --label epic --search "<feature>"`.
   - **jira**: check for an existing Jira ticket and fetch it for context.
   - **inkdrop**: `search-notes` for an existing Spec note.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the PRD, and respect any ADRs in the area you're touching.

2. Sketch out the major modules you will need to build or modify. Actively look for opportunities to extract deep modules that can be tested in isolation.

A deep module (as opposed to a shallow module) is one which encapsulates a lot of functionality in a simple, testable interface which rarely changes.

Check with the user that these modules match their expectations, and which modules they want tests written for.

3. Write the PRD using the template below, then save it following the publish rules.

<prd-template>

## Overview

The problem the user is facing and the context, from the user's perspective. 2-4 sentences.

## Goals

- The outcomes this delivers.

## Non-goals

What is explicitly out of scope for this PRD.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories, in the format:

1. As an <actor>, I want a <feature>, so that <benefit>

This list should be extensive and cover all aspects of the feature.

## Implementation Decisions

Modules to build/modify, their interfaces, technical clarifications, architectural decisions, schema changes, API contracts, specific interactions.

Do NOT include specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note it came from a prototype. Trim to the decision-rich parts.

## Testing Decisions

What makes a good test (test external behavior, not implementation details), which modules will be tested, and prior art for the tests in the codebase.

## Open questions

- [ ] Anything unresolved.

## Tasks

<!-- github only: sub-issues auto-render here with a progress bar once /to-issues runs. Leave this placeholder; do not maintain by hand. -->
_(populated by /to-issues after breakdown)_

## References

Links to related issues, PRs, ADRs, or external docs.

</prd-template>

## Publish

### github (default)

1. Ensure the `epic` label exists (create on-demand, ignore error if present):
   `gh label create epic --color 5319E7 --description "Tracking issue / PRD" 2>/dev/null || true`
2. Write the PRD body to a temp file. The Epic does **not** get a milestone (it may span releases).
3. If an Epic already exists for this feature: `gh issue edit <n> --body-file <tmp>`.
   Otherwise: `gh issue create --label epic --title "<feature>" --body-file <tmp>`.
4. Report the created/updated issue number — `/to-issues` will attach sub-issues to it.

### jira

- If a Jira ticket already exists: update the ticket **description** with the PRD. Never add it as a comment.
- If no ticket exists: create one via the Jira MCP tools with the PRD as the description.
- Do **not** create a parallel note for the PRD.

### inkdrop

- Find the project's `Spec` notebook (under the project notebook in Solo Dev) via `list-notebooks`.
- If a Spec note exists for this feature: update it. Otherwise create one. Title = feature name, no type/release tags, status = `none`.
