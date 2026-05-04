## Why

`/github:create-pr` currently blocks when local work is on `main`, but that is the normal default state before Codex starts publishing work. The workflow should create and switch to a safe `codex/` branch automatically unless the human already selected a work branch.

## What Changes

- Teach `/github:create-pr` to create and switch to a `codex/<issue-or-change-slug>` branch when the current branch is `main` or the default base branch.
- Preserve existing behavior when the human already switched to a non-main work branch.
- Define safe staging, commit, push, and draft PR creation expectations for local changes.
- Keep blockers for missing issue linkage, unclear repository context, unsafe branch publication, omitted verification, or omitted residual risk.

## Capabilities

### New Capabilities

### Modified Capabilities

- `github-orchestration-skills`: `/github:create-pr` branch creation, switch, commit, push, and PR creation requirements.

## Impact

- Affected skill instruction: `.codex/skills/github-create-pr/SKILL.md`.
- Affected spec: `openspec/specs/github-orchestration-skills/spec.md`.
- No runtime dependency or GitHub Actions change expected.
