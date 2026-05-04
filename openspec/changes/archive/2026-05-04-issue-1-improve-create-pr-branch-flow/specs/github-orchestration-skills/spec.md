## MODIFIED Requirements

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
