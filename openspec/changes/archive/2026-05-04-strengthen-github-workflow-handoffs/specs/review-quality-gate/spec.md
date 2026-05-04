## MODIFIED Requirements

### Requirement: Preserve human archive acceptance
Archive SHALL present review verdict, residual risk, and GitHub delivery evidence before final archive action so the human owns final risk acceptance.

#### Scenario: Review and GitHub evidence pass before archive
- **WHEN** archive starts and review gate checks pass for a GitHub-backed change
- **THEN** archive presents the review verdict, residual risk, PR link or merge record, and issue linkage before finalizing the change

#### Scenario: Local-only archive exception is documented
- **WHEN** archive starts for a change where the human explicitly accepted local-only work
- **THEN** archive presents the review verdict, residual risk, and documented GitHub bypass reason before finalizing the change

### Requirement: Require GitHub delivery evidence before archive
Archive SHALL block GitHub-backed changes unless durable GitHub PR or merge evidence is available.

#### Scenario: GitHub-backed change has merge evidence
- **WHEN** archive starts for a GitHub-backed change with passing review metadata and durable PR or merge evidence
- **THEN** archive may continue to remaining archive checks

#### Scenario: GitHub-backed change lacks merge evidence
- **WHEN** archive starts for a GitHub-backed change without durable PR or merge evidence
- **THEN** archive is blocked with instructions to run `/github:merge <pr>` or document an explicit local-only exception

#### Scenario: Review passes but GitHub evidence is missing
- **WHEN** `/review` has passed but no PR, merge record, or accepted local-only bypass exists
- **THEN** archive remains blocked because local review evidence alone is insufficient for GitHub-backed delivery
