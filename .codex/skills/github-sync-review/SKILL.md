---
name: github:sync-review
description: Read GitHub PR review state, inline comments, general comments, requested changes, and checks, then produce a read-only action plan and handoff.
license: MIT
compatibility: Requires GitHub MCP/plugin access.
---

# GitHub Sync Review

Use this skill when the user wants to pull GitHub PR review state into the local workflow before deciding what to fix next.

## Shared Contract

- Use the GitHub MCP/plugin for GitHub PR, review, comment, and check reads.
- Do not ask the user to paste tokens, personal access tokens, or secrets into chat.
- If GitHub MCP/plugin is unavailable or lacks permission, stop and produce a blocked handoff.
- Do not create a custom GitHub HTTP client.
- This skill is read-only. It must not edit files, reply to comments, resolve threads, push commits, update PRs, or mutate GitHub state.

## Input

Accept any of:

- PR number or URL
- repository identifier
- current branch PR context

If PR target is unclear, ask for the PR.

## Workflow

1. Resolve PR target.
2. Read PR metadata through GitHub MCP/plugin:
   - title
   - URL
   - branch
   - linked issues when available
   - review state
3. Read review feedback:
   - inline file comments
   - general PR comments
   - general review comments
   - requested changes
   - approvals
   - resolved/outdated state when available
4. Read CI/check state when exposed by GitHub MCP/plugin.
   - If no required checks or statuses are exposed for the PR head commit, classify checks as N/A instead of a blocker.
   - If required checks are failing or pending, classify them as CI/check blockers.
5. Classify feedback:
   - blocking actionable
   - non-blocking actionable
   - informational
   - ambiguous
   - already resolved or outdated
   - CI/check blocker
   - checks N/A
6. Produce an action plan.
7. Return the handoff.

## Output Shape

```md
## PR Review Sync

### PR State

### Blocking Items

### Actionable Comments

### Ambiguous Items

### CI / Checks

### Recommended Next Step
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

Typical next command:

```text
/github:address-comments <pr>
```

If clean:

```text
/github:merge <pr>
```

## Blocked States

Block instead of guessing when:

- PR target is unclear
- GitHub MCP/plugin is unavailable
- review/comment/check data cannot be read
- connector data is too incomplete to distinguish resolved from unresolved review feedback

If data is incomplete but useful, say exactly what is missing in `Blockers` or `Residual risk`.
