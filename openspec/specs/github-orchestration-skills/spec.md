## Purpose
Define GitHub orchestration skills, handoff contracts, and read-only versus mutating workflow boundaries around the OpenSpec workflow.
## Requirements
### Requirement: GitHub orchestration skills
WorkLoop SHALL provide dedicated GitHub orchestration skill instructions that operate around the existing OpenSpec workflow.

#### Scenario: Required skill set exists
- **WHEN** the GitHub orchestration workflow is implemented
- **THEN** WorkLoop MUST include skill instructions for `/github:create-issue`, `/github:create-pr`, `/github:post-review`, `/github:sync-review`, `/github:address-comments`, and `/github:merge`

#### Scenario: Core skills hand off to orchestration skills
- **WHEN** core OpenSpec skills finish stages that require GitHub durability
- **THEN** those skills MUST hand off to the appropriate GitHub orchestration skill instead of bypassing GitHub gates

#### Scenario: Create PR starts on main
- **WHEN** `/github:create-pr` starts from `main` or the default base branch with publishable local work
- **THEN** it MUST create and switch to a `codex/<issue-or-change-slug>` branch before committing, pushing, or creating the PR

#### Scenario: Create PR starts on existing work branch
- **WHEN** `/github:create-pr` starts from a non-main work branch selected by the human
- **THEN** it MUST preserve that branch and use it as the PR head branch unless the branch is unsafe to publish

#### Scenario: Create PR publishes local work
- **WHEN** `/github:create-pr` has a safe work branch, issue linkage, OpenSpec change path, verification, residual risk, and publishable local changes
- **THEN** it MUST stage scoped changes, create an intentional commit, push the branch, and create or update a draft PR through GitHub MCP/plugin

#### Scenario: Create PR cannot publish safely
- **WHEN** repository context, issue linkage, branch safety, scoped local changes, verification, or residual risk is unclear
- **THEN** `/github:create-pr` MUST block with a handoff instead of committing, pushing, or creating a misleading PR

### Requirement: Handoff contract
Every GitHub orchestration skill SHALL output a handoff that tells the human current state, completed work, required human action, next command, blockers, and links.

#### Scenario: Successful GitHub step
- **WHEN** a GitHub orchestration skill completes successfully
- **THEN** its output MUST include `Current state`, `Done`, `Needs human`, `Next command`, `Blockers`, and `Links`

#### Scenario: Blocked GitHub step
- **WHEN** a GitHub orchestration skill cannot complete because of missing context, missing permission, unavailable GitHub access, CI failure, or ambiguous review feedback
- **THEN** it MUST still output the same handoff fields and identify the blocker explicitly

### Requirement: End-to-end GitHub handoff order
WorkLoop SHALL document and enforce the expected GitHub-backed handoff sequence around OpenSpec work while keeping mechanical operations AI-native.

#### Scenario: Standard GitHub-backed workflow
- **WHEN** a tracked WorkLoop change moves from discovery to archive
- **THEN** the workflow MUST route through `/explore`, `/github:create-issue`, `/brief` or `/propose`, `/tdd`, `/apply`, `/github:create-pr`, `/review`, `/github:post-review`, `/github:sync-review`, `/github:address-comments` when needed, `/github:merge`, and `/archive`

#### Scenario: Apply completes for a GitHub-backed change
- **WHEN** `/apply` completes all tasks for a change with GitHub issue, PR, or GitHub-backed workflow context
- **THEN** the next handoff MUST be `/github:create-pr <change-name>` instead of `/review <change-name>`

#### Scenario: Apply completes for a local-only change
- **WHEN** `/apply` completes all tasks for a change with an explicitly accepted local-only exception
- **THEN** the next handoff MAY be `/review <change-name>` before archive

#### Scenario: Review feedback is clean
- **WHEN** `/github:sync-review` finds no unresolved comments, requested changes, or failing required checks
- **THEN** the next handoff MUST be `/github:merge`

#### Scenario: Review feedback requires changes
- **WHEN** `/github:sync-review` finds actionable unresolved feedback
- **THEN** the next handoff MUST be `/github:address-comments` before merge

#### Scenario: Draft PR is ready
- **WHEN** `/github:merge` finds that a draft PR satisfies review, comment, check, issue, and local review gates
- **THEN** it MUST mark the PR ready for review before merging instead of blocking for manual draft handling

#### Scenario: GitHub reviewer approval is not required
- **WHEN** branch protection or repository policy does not require GitHub reviewer approval
- **THEN** `/github:merge` MUST NOT block only because no GitHub approval review exists

#### Scenario: Human release approval is required
- **WHEN** `/github:merge` is ready to merge a GitHub-backed change
- **THEN** it MUST require explicit human release approval unless that approval was already provided in the merge request

#### Scenario: CI is not configured
- **WHEN** GitHub exposes no required checks or statuses for the PR head commit
- **THEN** `/github:merge` MUST treat checks as not applicable and record that state instead of blocking as unreadable CI

#### Scenario: Required CI fails
- **WHEN** GitHub exposes required checks or statuses and any required check is failing or pending
- **THEN** `/github:merge` MUST block until checks pass or the human explicitly accepts a documented bypass

### Requirement: MCP GitHub usage
GitHub orchestration skills SHALL use the MCP GitHub/plugin path for GitHub operations and SHALL NOT ask users to paste tokens or secrets into chat.

#### Scenario: GitHub operation requested
- **WHEN** a GitHub orchestration skill needs to read or mutate GitHub state
- **THEN** the skill MUST use the available MCP GitHub/plugin capability or fail with a handoff blocker

#### Scenario: Token provided in chat
- **WHEN** a user attempts to provide a personal token or secret in chat
- **THEN** the skill MUST reject chat-based secret handling and direct the user to use connector, environment, or configuration-based setup

### Requirement: Merge verifies acceptance criteria closure
`/github:merge` SHALL verify linked issue acceptance criteria coverage before merging or closing the linked issue for a GitHub-backed change.

#### Scenario: Merge reads linked issue criteria
- **WHEN** `/github:merge` resolves a linked GitHub issue with a `## Acceptance Criteria` section
- **THEN** it MUST read the issue body and identify parseable checked and unchecked criteria before merge or close

#### Scenario: Merge maps criteria to evidence
- **WHEN** `/github:merge` evaluates linked issue acceptance criteria
- **THEN** it MUST map each criterion to durable evidence such as `review.md`, `tasks.md`, generated tests, test or verification output, PR verification notes, or explicit documented human bypass

#### Scenario: Merge verifies checked criteria
- **WHEN** `/github:merge` finds a criterion that is already checked in the linked issue body
- **THEN** it MUST still map that criterion to durable evidence or explicit documented human bypass before merge or close

#### Scenario: Merge updates verified criteria
- **WHEN** `/github:merge` finds sufficient evidence for an unchecked acceptance criterion
- **THEN** it MUST update the linked issue body so that criterion is checked before merge or close

#### Scenario: Merge verifies issue body updates
- **WHEN** `/github:merge` updates acceptance criteria checkbox state in the linked issue body
- **THEN** it MUST re-read the issue body and verify the expected checkbox state before merge or close

#### Scenario: Merge blocks uncovered criteria
- **WHEN** `/github:merge` finds criteria without sufficient evidence or explicit documented bypass
- **THEN** it MUST block merge and issue close and return a handoff listing the uncovered criteria

#### Scenario: Merge reports ambiguous criteria
- **WHEN** `/github:merge` cannot parse acceptance criteria or cannot determine which evidence covers a criterion
- **THEN** it MUST report the ambiguity in the handoff and block merge and close unless the human explicitly accepts and durably documents a bypass

#### Scenario: Merge reports uneditable criteria
- **WHEN** `/github:merge` cannot update or verify the linked issue body after update
- **THEN** it MUST report the issue body edit failure in the handoff and block merge and close unless the human explicitly accepts and durably documents a bypass

#### Scenario: Merge avoids blind checkbox updates
- **WHEN** PR checks, review metadata, or GitHub review state pass
- **THEN** `/github:merge` MUST NOT blindly check all issue acceptance criteria without criterion-level evidence mapping

#### Scenario: Merge records criterion coverage
- **WHEN** `/github:merge` completes acceptance criteria verification
- **THEN** its handoff MUST summarize covered, already checked, uncovered, ambiguous, unparseable, uneditable, and bypassed criteria with evidence links or paths when available

