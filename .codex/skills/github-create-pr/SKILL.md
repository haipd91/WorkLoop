---
name: github:create-pr
description: Create or update a draft GitHub PR from the current work branch with issue link, OpenSpec path, summary, verification, residual risk, and handoff.
license: MIT
compatibility: Requires GitHub MCP/plugin access and local git context.
---

# GitHub Create PR

Use this skill after local work exists and the user wants to publish it to GitHub as a draft PR.

## Shared Contract

- Use the GitHub MCP/plugin for GitHub PR operations.
- Do not ask the user to paste tokens, personal access tokens, or secrets into chat.
- If GitHub MCP/plugin is unavailable or lacks permission, stop and produce a blocked handoff.
- Do not create a custom GitHub HTTP client.
- This is a mutating GitHub skill. State whether it will create or update a PR.

## Input

Accept any of:

- OpenSpec change name
- issue number or URL
- current branch
- PR title/body hints
- verification notes

If issue link or OpenSpec change path cannot be identified, ask the human or block before creating/updating a misleading PR.

## Workflow

1. Resolve local repository, current branch, and remote.
2. Resolve issue link:
   - from user input
   - from branch name
   - from OpenSpec artifacts
   - from existing PR metadata
3. Resolve OpenSpec change path:
   - `openspec/changes/<change-name>`
4. Inspect local state enough to summarize:
   - changed files
   - completed tasks
   - tests or verification evidence
   - residual risk
5. Push branch only if the user requested publishing and the local git state is ready.
6. Create or update a draft PR through GitHub MCP/plugin.
7. Return the handoff.

## PR Body Shape

```md
## Problem

## Approach

## Verification

## OpenSpec

## Residual Risk

## Links
```

Required content:

- linked issue
- OpenSpec change path
- problem
- approach
- verification
- residual risk, even if `none`

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

Typical next command:

```text
/review <change-name>
```

or, if PR feedback should be pulled first:

```text
/github:sync-review <pr>
```

## Blocked States

Block instead of guessing when:

- repository or branch is unclear
- issue link is missing and the user has not accepted that state
- OpenSpec change path is missing
- GitHub MCP/plugin is unavailable
- branch cannot be published safely
- PR body would omit verification or residual risk
