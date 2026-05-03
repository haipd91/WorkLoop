## Why

WorkLoop currently has an OpenSpec-first delivery workflow, but it does not define how GitHub Issues and Pull Requests act as the durable source of truth. Without that contract, Codex work can drift across chat context, local artifacts, issues, and PRs without a consistent lifecycle.

This change establishes a GitHub-first operating model while preserving OpenSpec as the structured planning and quality-gate artifact set committed to the repository.

## What Changes

- Define GitHub Issues and Pull Requests as the source of truth for WorkLoop delivery state.
- Document the lifecycle from issue intake through OpenSpec change creation, branch/PR work, review, merge, and archive.
- Define the responsibilities of GitHub Issues, branches, commits, PRs, and OpenSpec artifacts.
- Define naming conventions for issues, changes, branches, and PRs.
- Define artifact ownership rules so durable decisions live in GitHub or repository files, not only in chat.
- Define quality gates before PR readiness, merge, and OpenSpec archive.
- Explicitly mark GitHub automation, skill behavior changes, GitHub Actions, and issue-template migration as future scope.

## Capabilities

### New Capabilities

- `github-workflow`: Defines GitHub source-of-truth workflow requirements, lifecycle, artifact ownership, naming conventions, and quality gates.

### Modified Capabilities

- None.

## Impact

- Adds documentation/specification artifacts for GitHub-first WorkLoop operation.
- Does not change application code, skill behavior, GitHub automation, GitHub Actions, or repository templates.
- Provides the contract future skill and automation changes can implement against.
