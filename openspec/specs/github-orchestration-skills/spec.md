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
