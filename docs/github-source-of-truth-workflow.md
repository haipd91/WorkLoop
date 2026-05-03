# GitHub Source Of Truth Workflow

## 1. Purpose

- GitHub repo is the durable source of truth for WorkLoop delivery state.
- OpenSpec remains the structured artifact system committed inside the repo.
- Chat is only an interface. Material decisions must be captured in GitHub or repo files.

This workflow defines how GitHub Issues, branches, commits, Pull Requests, and OpenSpec artifacts work together.

For the operational skill flow that creates issues, opens PRs, syncs review comments, addresses feedback, merges PRs, and hands off to archive, see `docs/github-orchestration-workflow.md`.

## 2. Source Of Truth Model

```text
GitHub repo
  |
  |-- Issues: demand, priority, backlog, user-visible intent
  |-- Branches: isolated work units
  |-- Commits: auditable change history
  |-- Pull Requests: review, verification, delivery evidence
  `-- OpenSpec artifacts: scope, specs, design, tasks, tests, review
```

- GitHub owns tracking and delivery state.
- OpenSpec owns structured reasoning and gates.
- Durable decisions must live in one of:
  - GitHub issue
  - GitHub PR
  - committed OpenSpec artifact
  - committed docs/spec file

Chat-only decisions are not sufficient for review readiness.

## 3. Lifecycle

```text
Issue
  |
  v
OpenSpec change
  |
  v
Branch
  |
  v
Implementation + commits
  |
  v
Draft PR
  |
  v
Review + verification
  |
  v
Merge
  |
  v
OpenSpec archive
```

### 3.1 Issue intake

- GitHub Issues are the primary intake artifact for:
  - product/workflow changes
  - bugs
  - durable follow-up work
  - decisions that need tracking
- A usable issue should include:
  - problem
  - expected outcome
  - scope or non-scope
  - acceptance criteria
  - relevant links/context

If an issue lacks enough context or acceptance criteria, clarify or add context before creating implementation tasks.

### 3.2 OpenSpec change

- Create an OpenSpec change when issue-backed work needs structured delivery.
- The change should preserve a stable connection to the issue or outcome.
- OpenSpec artifacts should capture:
  - `brief.md`: problem, goal, scope, constraints, acceptance criteria
  - `proposal.md`: why, what changes, capability impact
  - `specs/**/*.md`: normative requirements and scenarios
  - `design.md`: decisions, alternatives, risks
  - `tasks.md`: small verifiable work items
  - `tests/`: runnable tests or scenario tests
  - `review.md`: findings, verification, residual risk, verdict

### 3.3 Branch and commits

- Work should happen on a branch tied to the issue or OpenSpec change.
- Commits should preserve useful audit history.
- Commits should not be the only place where scope, risk, or design decisions are recorded.

### 3.4 Pull Request

- PRs are the delivery and review artifact.
- PR body should include:
  - problem
  - approach
  - verification
  - linked issue
  - OpenSpec change path
  - residual risk, if any
- Draft PR is the default while work or verification is incomplete.

### 3.5 Review, merge, archive

- Review happens in the PR and in `review.md`.
- Merge records delivery in GitHub.
- Archive records OpenSpec completion after review gates pass.

## 4. Artifact Ownership

| Artifact | Owns | Does not own |
| --- | --- | --- |
| GitHub Issue | Demand, priority, user-visible intent, acceptance criteria, blockers | Full design/spec detail |
| OpenSpec `brief.md` | Structured problem, goal, scope, constraints, acceptance criteria | Review decision |
| OpenSpec `proposal.md` | Why, scope, capability impact | Implementation detail |
| OpenSpec `specs/**/*.md` | Requirements and scenarios | Task execution state |
| OpenSpec `design.md` | Technical decisions, alternatives, risks | PR review outcome |
| OpenSpec `tasks.md` | Implementation checklist | Source-of-truth backlog priority |
| OpenSpec `tests/` | Test intent or runnable tests | Final review verdict |
| Git branch | Isolated work surface | Durable decision log |
| Commits | Auditable change history | Full issue context |
| Pull Request | Review, verification, delivery evidence | Backlog priority |
| OpenSpec `review.md` | Quality gate result, findings, residual risk | GitHub merge state |

Avoid duplicating full OpenSpec content into issue or PR bodies. Link to committed files instead.

## 5. Naming Conventions

### 5.1 Issue references

- Use GitHub issue numbers when available: `#123`.
- Use close/fix syntax only when the PR actually closes the issue:
  - `Fixes #123`
  - `Closes #123`
- If a PR only relates to an issue, use plain reference text:
  - `Related to #123`

### 5.2 OpenSpec changes

- Use kebab-case.
- Preserve the issue or outcome connection where practical.
- Examples:
  - `issue-123-add-github-workflow`
  - `github-source-of-truth-workflow`

### 5.3 Branches

- Use the `codex/` prefix.
- Include an issue number or stable change slug when practical.
- Examples:
  - `codex/issue-123-add-github-workflow`
  - `codex/github-source-of-truth-workflow`

### 5.4 Commits

- Keep commit messages outcome-focused.
- Mention the issue when useful:
  - `Document GitHub source-of-truth workflow (#123)`
- Do not hide scope changes or risk acceptance only in commit messages.

### 5.5 Pull Requests

- PR title should state the outcome:
  - `Document GitHub source-of-truth workflow`
  - `Fix #123: document GitHub workflow contract`
- PR body should include:

```md
## Problem

## Approach

## Verification

## OpenSpec

## Links
```

## 6. Quality Gates

### 6.1 PR readiness

Before marking a PR ready for review:

- Issue or PR explains the problem and expected outcome.
- OpenSpec artifacts exist when the change needs structured delivery.
- Scope, requirements, implementation plan, and verification evidence are represented in GitHub or committed repo files.
- Material decisions are not chat-only.
- Out-of-scope work is explicit.

### 6.2 Merge readiness

Before merge:

- PR review is complete.
- Required verification has run, or skipped verification is explained as residual risk.
- OpenSpec task checklist is complete when a change uses OpenSpec.
- PR body links the issue and OpenSpec change path.
- Any unresolved follow-up work is tracked as issue, PR note, or OpenSpec artifact.

### 6.3 Archive readiness

Before archiving an OpenSpec change:

- `review.md` exists and has a passing verdict.
- Blocking findings are resolved or explicitly accepted by the human.
- Merge or PR record provides durable delivery evidence.
- Specs, docs, and tasks reflect the delivered behavior.

## 7. Future Scope

This contract does not implement automation.

Follow-up work should be split into separate GitHub issues or OpenSpec changes:

- GitHub issue and PR templates.
- Standard GitHub labels for issue lifecycle and PR review state.
- Skill behavior updates for `/brief`, `/propose`, `/apply`, `/review`, `/archive`.
- GitHub Actions for validation or PR checks.
- Automatic status sync between OpenSpec tasks and GitHub comments, labels, or Projects.

Do not mix these follow-ups into the docs/spec-only contract unless a new change explicitly scopes them in.
