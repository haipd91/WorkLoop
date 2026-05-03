## ADDED Requirements

### Requirement: GitHub orchestration skills
WorkLoop SHALL provide dedicated GitHub orchestration skill instructions that operate around the existing OpenSpec workflow without rewriting the core OpenSpec skills.

#### Scenario: Required skill set exists
- **WHEN** the GitHub orchestration workflow is implemented
- **THEN** WorkLoop MUST include skill instructions for `/github:create-issue`, `/github:create-pr`, `/github:post-review`, `/github:sync-review`, `/github:address-comments`, and `/github:merge`

#### Scenario: Core skills remain separate
- **WHEN** GitHub orchestration skills are added
- **THEN** the implementation MUST NOT rewrite `/brief`, `/propose`, `/tdd`, `/apply`, `/review`, or `/archive` to own the GitHub orchestration flow

### Requirement: MCP GitHub usage
GitHub orchestration skills SHALL use the MCP GitHub/plugin path for GitHub operations and SHALL NOT ask users to paste tokens or secrets into chat.

#### Scenario: GitHub operation requested
- **WHEN** a GitHub orchestration skill needs to read or mutate GitHub state
- **THEN** the skill MUST use the available MCP GitHub/plugin capability or fail with a handoff blocker

#### Scenario: Token provided in chat
- **WHEN** a user attempts to provide a personal token or secret in chat
- **THEN** the skill MUST reject chat-based secret handling and direct the user to use connector, environment, or configuration-based setup

### Requirement: Handoff contract
Every GitHub orchestration skill SHALL output a handoff that tells the human current state, completed work, required human action, next command, blockers, and links.

#### Scenario: Successful GitHub step
- **WHEN** a GitHub orchestration skill completes successfully
- **THEN** its output MUST include `Current state`, `Done`, `Needs human`, `Next command`, `Blockers`, and `Links`

#### Scenario: Blocked GitHub step
- **WHEN** a GitHub orchestration skill cannot complete because of missing context, missing permission, unavailable MCP GitHub, CI failure, or ambiguous review feedback
- **THEN** it MUST still output the same handoff fields and identify the blocker explicitly

### Requirement: Create issue workflow
`/github:create-issue` SHALL create a GitHub issue from work information and return the issue number, issue URL, and a suggested OpenSpec change name.

#### Scenario: Issue created from work description
- **WHEN** a user provides enough work information to create an issue
- **THEN** `/github:create-issue` MUST create the issue through MCP GitHub/plugin and hand off the issue URL, issue number, suggested change name, and next OpenSpec command

#### Scenario: Issue input lacks acceptance criteria
- **WHEN** issue input lacks problem, goal, scope, or acceptance criteria
- **THEN** `/github:create-issue` MUST ask for clarification or create a clearly marked incomplete/draft issue only when the human explicitly accepts that state

### Requirement: Create PR workflow
`/github:create-pr` SHALL create or update a draft PR from the current work branch with issue linkage, OpenSpec change path, summary, verification, and residual risk.

#### Scenario: Draft PR created
- **WHEN** local work is ready to publish for review
- **THEN** `/github:create-pr` MUST create or update a draft PR through MCP GitHub/plugin and include issue link, OpenSpec path, problem, approach, verification, and residual risk in the PR body

#### Scenario: Missing issue or change path
- **WHEN** `/github:create-pr` cannot identify an issue link or OpenSpec change path
- **THEN** it MUST ask the human for the missing linkage or record the missing linkage as a blocker before creating/updating the PR

### Requirement: Post review workflow
`/github:post-review` SHALL post the local OpenSpec review result to the GitHub PR.

#### Scenario: Review artifact exists
- **WHEN** `review.md` exists for the OpenSpec change
- **THEN** `/github:post-review` MUST post the review verdict, blocking findings state, findings summary, tests run, residual risk, and review artifact link/path to the PR

#### Scenario: Review artifact missing
- **WHEN** no usable review artifact exists
- **THEN** `/github:post-review` MUST block and hand off the next command needed to produce review evidence

### Requirement: Sync review workflow
`/github:sync-review` SHALL be read-only and produce an action plan from GitHub PR review state, inline comments, general comments, requested changes, and CI/check status.

#### Scenario: PR has review feedback
- **WHEN** a PR has unresolved inline comments, general review comments, requested changes, or failing checks
- **THEN** `/github:sync-review` MUST summarize them by type, identify blockers, include links when available, and recommend the next command without mutating local files or GitHub state

#### Scenario: PR is clean
- **WHEN** a PR has no unresolved review comments, no requested changes, and passing checks
- **THEN** `/github:sync-review` MUST hand off that the PR is clean and identify the next review or merge command

### Requirement: Address comments workflow
`/github:address-comments` SHALL handle actionable inline file comments and general PR/review comments, make scoped local fixes when clear, verify changes, reply to comments when supported, and push the branch.

#### Scenario: Actionable comments are clear
- **WHEN** review comments are actionable and within scope
- **THEN** `/github:address-comments` MUST map comments to files or general tasks, apply focused fixes, run relevant verification, push the branch, and reply to inline or general comments when MCP GitHub/plugin supports it

#### Scenario: Comment is ambiguous or out of scope
- **WHEN** a review comment is ambiguous, conflicts with approved scope, or requires human product judgment
- **THEN** `/github:address-comments` MUST stop and ask the human instead of guessing

### Requirement: Merge workflow
`/github:merge` SHALL check release gates, merge the PR through MCP GitHub/plugin, close the issue when needed, and hand off to OpenSpec archive.

#### Scenario: Merge gates pass
- **WHEN** PR review state, CI/check state, local review evidence, and issue linkage satisfy the documented gates
- **THEN** `/github:merge` MUST merge the PR through MCP GitHub/plugin, close the issue if it was not auto-closed, and hand off `/archive <change-name>`

#### Scenario: Merge gates fail
- **WHEN** required review approval, passing checks, local `review.md`, issue linkage, or unresolved comment state is missing
- **THEN** `/github:merge` MUST block and hand off the specific missing gate without merging

### Requirement: Workflow documentation
WorkLoop SHALL document how GitHub orchestration skills fit around the OpenSpec workflow and distinguish core OpenSpec skills from read-only and mutating GitHub skills.

#### Scenario: Human reads workflow docs
- **WHEN** a human reads the workflow documentation
- **THEN** the docs MUST show the end-to-end flow: create issue -> OpenSpec workflow -> create PR -> post/sync review -> address comments -> merge -> archive

#### Scenario: Handoff docs exist
- **WHEN** a human reads the GitHub orchestration docs
- **THEN** the docs MUST include the handoff contract and examples for successful and blocked states
