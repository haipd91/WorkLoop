## 1. Spec Contract

- [x] 1.1 Update active `github-orchestration-skills` spec for `/github:create-pr` branch creation and publish behavior.
- [x] 1.2 Verify the delta spec includes main/default-branch, existing work branch, publish, and blocked-state scenarios.

## 2. Skill Behavior

- [x] 2.1 Update `.codex/skills/github-create-pr/SKILL.md` to create and switch to `codex/<issue-or-change-slug>` when starting on `main` or the default branch.
- [x] 2.2 Preserve existing non-main branch behavior when the human already selected a work branch.
- [x] 2.3 Add safe staging, commit, push, and draft PR creation guidance.
- [x] 2.4 Keep blockers for missing issue linkage, unclear scope, unsafe branch publication, missing verification, or missing residual risk.

## 3. Verification

- [x] 3.1 Generate test scenarios with `/tdd issue-1-improve-create-pr-branch-flow`.
- [x] 3.2 Run OpenSpec validation for `issue-1-improve-create-pr-branch-flow`.
- [x] 3.3 Search `/github:create-pr` guidance to verify it no longer blocks only because current branch is `main`.
