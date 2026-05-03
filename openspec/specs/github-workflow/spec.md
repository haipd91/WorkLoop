## Purpose
Define how GitHub Issues, Pull Requests, branches, commits, and OpenSpec artifacts form the durable WorkLoop delivery record.

## Requirements
### Requirement: GitHub source of truth
WorkLoop SHALL treat the GitHub repository as the durable source of truth for delivery state, including issue intent, pull request review state, committed OpenSpec artifacts, and merge/archive history.

#### Scenario: Durable workflow state
- **WHEN** a WorkLoop change is created from a GitHub-tracked work item
- **THEN** the issue, branch, PR, commits, and OpenSpec artifacts MUST provide enough durable context to understand the work without relying on chat history

#### Scenario: Chat-only decision rejected
- **WHEN** a material scope, design, or risk decision exists only in chat
- **THEN** the workflow MUST capture that decision in a GitHub issue, PR, or committed OpenSpec artifact before the work is considered ready for review

### Requirement: Issue intake lifecycle
WorkLoop SHALL define GitHub Issues as the primary intake and backlog artifact for user-visible work, defects, workflow changes, and durable follow-up items.

#### Scenario: Issue starts a change
- **WHEN** an issue is selected for structured delivery
- **THEN** the workflow MUST create or reference an OpenSpec change that captures scope, requirements, design, tasks, tests, and review artifacts as needed

#### Scenario: Explore precedes issue creation
- **WHEN** exploratory discussion identifies work worth tracking durably
- **THEN** the workflow MUST hand off to `/github:create-issue` before creating implementation-ready OpenSpec tasks unless the human explicitly accepts local-only work

#### Scenario: Issue lacks acceptance criteria
- **WHEN** an issue does not include enough problem context, expected outcome, or acceptance criteria
- **THEN** the workflow MUST ask for clarification or add missing context before creating implementation tasks

### Requirement: OpenSpec artifact ownership
WorkLoop SHALL define OpenSpec artifacts as repository-committed planning, specification, task, test, and review records that support GitHub-tracked delivery.

#### Scenario: Planning artifact ownership
- **WHEN** a change uses OpenSpec planning
- **THEN** proposal, specs, design, tasks, tests, and review artifacts MUST be committed to the repository as part of the branch or PR that represents the work

#### Scenario: Artifact role separation
- **WHEN** documenting workflow state
- **THEN** GitHub Issues MUST own demand and priority, Pull Requests MUST own review and delivery evidence, and OpenSpec artifacts MUST own structured scope, requirements, design, tasks, tests, and quality gates

### Requirement: Branch and pull request lifecycle
WorkLoop SHALL define branch and pull request conventions that connect GitHub work items to OpenSpec changes.

#### Scenario: Branch created for issue-backed work
- **WHEN** a branch is created for a GitHub issue
- **THEN** the branch name MUST include the `codex/` prefix and a stable issue or change slug when practical

#### Scenario: Pull request created for delivery
- **WHEN** a pull request is opened for a WorkLoop change
- **THEN** the PR body MUST summarize the problem, approach, verification, linked issue, and relevant OpenSpec change path

### Requirement: Quality gates
WorkLoop SHALL define quality gates before PR readiness, merge, and OpenSpec archive.

#### Scenario: PR readiness gate
- **WHEN** a PR is marked ready for review
- **THEN** the workflow MUST verify that scope, requirements, implementation plan, and verification evidence are represented in GitHub or committed OpenSpec artifacts

#### Scenario: Archive gate
- **WHEN** an OpenSpec change is archived after delivery
- **THEN** the workflow MUST verify that review requirements are satisfied and that the GitHub PR or merge record provides durable delivery evidence

#### Scenario: Local-only exception
- **WHEN** a change intentionally skips GitHub issue or PR tracking
- **THEN** the workflow MUST record explicit human acceptance and residual risk before review or archive treats the missing GitHub evidence as non-blocking
