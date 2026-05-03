---
name: github:create-issue
description: Create a GitHub issue from work information, then hand off the issue number, URL, and suggested OpenSpec change name for the next workflow step.
license: MIT
compatibility: Requires GitHub MCP/plugin access.
---

# GitHub Create Issue

Use this skill when the user wants to turn work information into a durable GitHub issue before starting or continuing an OpenSpec workflow.

## Shared Contract

- Use the GitHub MCP/plugin for GitHub operations.
- Do not ask the user to paste tokens, personal access tokens, or secrets into chat.
- If GitHub MCP/plugin is unavailable or lacks permission, stop and produce a blocked handoff.
- Do not create a custom GitHub HTTP client.
- This is a mutating GitHub skill. State the exact issue target before creating it when the repo is ambiguous.

## Input

Accept any of:

- work description from the user
- existing brief/problem/goal/scope text
- repository identifier or current repository context
- optional labels, assignee, milestone, or priority

If repository context is unclear, resolve from local git remote when safe. If still ambiguous, ask for the repository.

## Workflow

1. Resolve repository context.
2. Extract issue content:
   - problem
   - goal or expected outcome
   - scope or non-scope
   - acceptance criteria
   - relevant links/context
3. If problem, goal, scope, or acceptance criteria are missing:
   - ask one concise clarification; or
   - create a clearly marked incomplete/draft issue only when the human explicitly accepts that state.
4. Create the issue through GitHub MCP/plugin.
5. Derive a suggested OpenSpec change name:
   - prefer `issue-<number>-<short-kebab-slug>`
   - keep it short and outcome-based
6. Return the handoff.

## Issue Body Shape

```md
## Problem

## Goal

## Scope

## Acceptance Criteria

- [ ] ...

## Links / Context
```

## Handoff

Every result, including blocked states, must end with:

```md
## Handoff

- Current state:
- Done:
- Needs human:
- Next command:
- Blockers:
- Links:
```

Successful next command is usually:

```text
/brief <suggested-change-name>
```

## Blocked States

Block instead of guessing when:

- repository is unclear
- GitHub MCP/plugin is unavailable
- issue creation permission is missing
- required issue content is too vague and human did not accept draft/incomplete state
- user attempts to paste a token or secret into chat
