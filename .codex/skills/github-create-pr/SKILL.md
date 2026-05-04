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

1. Resolve local repository, current branch, default base branch, and remote.
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
5. Resolve the PR head branch:
   - If current branch is `main` or the default base branch, derive a branch name before staging or committing.
   - Preferred format: `codex/issue-<number>-<short-change-slug>`.
   - If no issue number exists but missing linkage was explicitly accepted, use `codex/<change-slug>`.
   - If the derived branch already exists and points to the intended work, switch to it.
   - If the derived branch exists but points to unrelated work, block instead of force-updating it.
   - If current branch is a non-main work branch selected by the human, preserve it as the PR head branch unless it is unsafe to publish.
6. Stage, commit, and push only when scope is clear:
   - Stage only files that belong to the resolved OpenSpec change and requested implementation.
   - Do not stage unrelated dirty or untracked files.
   - Create an intentional commit after verification and residual risk are known.
   - Push the PR head branch after the commit succeeds.
7. Create or update a draft PR through GitHub MCP/plugin.
8. Return the handoff.

## Branch and Publish Rules

- Starting from `main` or the default base branch is supported. It is not a blocker by itself.
- Do not publish directly from `main` or the default base branch.
- Create or switch to the safe `codex/` work branch before staging, committing, pushing, or creating the PR.
- Preserve an existing non-main work branch when the human already selected it.
- If branch safety is unclear, block with a handoff before any commit or push.
- If unrelated changes exist and cannot be separated safely, block with a handoff.
- If unrelated changes can be clearly separated, stage explicit paths only and mention ignored local changes in the handoff.

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
- current branch is `main` or the default base branch and a safe `codex/` branch cannot be created or selected
- derived `codex/` branch exists but points to unrelated work
- dirty worktree contains unrelated changes that cannot be separated safely
- no scoped local changes are available to commit or PR body would misrepresent the work
- PR body would omit verification or residual risk
