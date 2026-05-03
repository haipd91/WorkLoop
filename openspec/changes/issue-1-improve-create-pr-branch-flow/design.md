## Context

The current `/github:create-pr` skill assumes the user or agent already prepared a publishable branch. In practice, Codex often starts from `main` with local OpenSpec and skill changes. Blocking at that point creates manual work and weakens the new GitHub-backed handoff flow.

Issue: https://github.com/haipd91/WorkLoop/issues/1

## Goals / Non-Goals

**Goals:**

- Make `main` a supported starting point for `/github:create-pr`.
- Automatically create and switch to a `codex/` branch when needed.
- Preserve a human-selected work branch.
- Require safe commit and push conditions before PR creation.
- Keep PR body requirements: issue link, OpenSpec path, problem, approach, verification, residual risk.

**Non-Goals:**

- Create a general-purpose git workflow manager.
- Auto-resolve unrelated dirty worktree changes.
- Change `/github:create-issue`, `/review`, `/github:merge`, or `/archive` behavior.

## Decisions

### Decision: `main` means create a branch, not block

If current branch is `main` or the repository default base branch, `/github:create-pr` should derive a branch name from the issue and change name, then switch to it before staging or committing.

Preferred branch format:

```text
codex/issue-<number>-<short-change-slug>
```

Alternative considered: keep blocking on `main`. Rejected because `main` is the expected pre-publish state.

### Decision: Existing work branch wins

If the human already switched to a non-main branch, `/github:create-pr` should use that branch unless it is unsafe to publish.

Alternative considered: always create a new branch. Rejected because it would ignore human branch setup and may split work.

### Decision: Commit and push only when scope is clear

The skill may stage, commit, and push only after it can identify the OpenSpec change path, issue link, changed files, verification, and residual risk. If unrelated changes exist, it must block or require explicit human acceptance.

Alternative considered: push whatever is dirty. Rejected because this can publish unrelated user work.

## Risks / Trade-offs

- [Risk] Branch name collision -> Mitigation: detect existing branch and use it when it points to the intended work, otherwise block.
- [Risk] Unrelated dirty changes get committed -> Mitigation: inspect changed files and block when scope is unclear.
- [Risk] Missing verification gets hidden in PR body -> Mitigation: block when verification or residual risk would be omitted.
- [Risk] Issue linkage is missing -> Mitigation: block unless the human explicitly accepts missing linkage.

## Migration Plan

1. Update `/github:create-pr` instructions with branch selection, commit, push, and PR creation flow.
2. Update `github-orchestration-skills` spec.
3. Add test scenarios for starting on `main`, existing branch, unsafe dirty state, and missing issue/verification.
4. Validate the change.

## Resolved Questions

- `/github:create-pr` creates or switches branch after resolving repository, issue linkage, OpenSpec path, and local scope, but before staging or committing.
- Commit messages should be intentional and related to the issue/change; user-provided commit hints may be used when they do not omit issue/change context.
