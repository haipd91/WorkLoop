## MODIFIED Requirements

### Requirement: Quality gates
WorkLoop SHALL define quality gates before PR readiness, merge, and OpenSpec archive, with Codex owning operational execution and the human owning final material risk or release approval.

#### Scenario: PR readiness gate
- **WHEN** a PR is marked ready for review
- **THEN** the workflow MUST verify that scope, requirements, implementation plan, and verification evidence are represented in GitHub or committed OpenSpec artifacts

#### Scenario: Archive gate
- **WHEN** an OpenSpec change is archived after delivery
- **THEN** the workflow MUST verify that review requirements are satisfied and that the GitHub PR or merge record provides durable delivery evidence

#### Scenario: Local-only exception
- **WHEN** a change intentionally skips GitHub issue or PR tracking
- **THEN** the workflow MUST record explicit human acceptance and residual risk before review or archive treats the missing GitHub evidence as non-blocking

#### Scenario: Human approves release risk
- **WHEN** Codex has completed issue, PR, review, sync, and merge gate checks for a GitHub-backed change
- **THEN** the human approval needed to proceed MUST be limited to material risk or release acceptance unless GitHub branch protection requires a separate reviewer approval

#### Scenario: Codex owns mechanical workflow steps
- **WHEN** a workflow step is mechanical and all required gates are satisfied
- **THEN** Codex MUST perform that step without asking the human to operate GitHub or OpenSpec manually
