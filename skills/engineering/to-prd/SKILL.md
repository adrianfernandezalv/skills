---
name: to-prd
description: Turn the current conversation context into a PRD and save it. Use when user wants to create a PRD from the current context.
---

This skill takes the current conversation context and codebase understanding and produces a PRD. Do NOT interview the user — just synthesize what you already know.

## Setup

Before starting:

1. Check if there is an existing Inkdrop note for this task (use `search-notes`). If found, read it and update it — do not create a duplicate.
2. Check if `CLAUDE.local.md` exists at the project root. If it contains `output: jira`, this is a work project — check if there is an existing Jira ticket and fetch it for context.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the PRD, and respect any ADRs in the area you're touching.

2. Sketch out the major modules you will need to build or modify to complete the implementation. Actively look for opportunities to extract deep modules that can be tested in isolation.

A deep module (as opposed to a shallow module) is one which encapsulates a lot of functionality in a simple, testable interface which rarely changes.

Check with the user that these modules match their expectations. Check with the user which modules they want tests written for.

3. Write the PRD using the template below, then save it following the output rules.

<prd-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this PRD.

## Further Notes

Any further notes about the feature.

</prd-template>

## Output rules

**Personal project** (no `CLAUDE.local.md` or no `output: jira`):
- If an Inkdrop note already exists: add a `## PRD` section with the PRD content. Update the note status to `active`.
- If no note exists: create a new Inkdrop note following the note-taking.md format. Ask the user which notebook to use. Set status to `none`.

**Work project** (`output: jira` in `CLAUDE.local.md`):
- If a Jira ticket already exists: add the PRD as a comment or update the description. Note the Jira ticket URL in the Inkdrop note under `## Context`.
- If no Jira ticket exists: create one using the Jira MCP tools with the PRD as the description.
- Always maintain an Inkdrop note as the personal working layer (context, handoffs, outcome).
