## MODIFIED Requirements

### Requirement: Define parseable review artifact contract
The review artifact SHALL include deterministic machine-checkable metadata for the reviewed implementation state and required human-readable sections.

#### Scenario: Review artifact is valid
- **WHEN** `/review` writes `openspec/changes/<name>/review.md`
- **THEN** the artifact includes `verdict`, `blocking_findings`, `reviewed_ref`, `reviewed_diff`, `reviewed_implementation_diff`, `reviewed_paths_excluded`, and `reviewed_at` metadata
- **THEN** `reviewed_paths_excluded` MUST be exactly `openspec/changes/<name>/review.md`
- **THEN** the artifact includes Findings, Tests Run, Residual Risk, and Verdict sections

#### Scenario: Review artifact is malformed
- **WHEN** archive starts and `review.md` is missing required metadata, has duplicate or contradictory metadata, or uses unknown values
- **THEN** archive is blocked with the malformed review condition as the reason

### Requirement: Block archive on stale review
Archive SHALL be blocked when the implementation state differs from the implementation state recorded in the review artifact, but SHALL NOT treat review-artifact-only commits as stale implementation changes.

#### Scenario: Review matches current implementation state
- **WHEN** archive starts and the current implementation state matches the implementation state recorded in `review.md`
- **THEN** archive may continue to the remaining archive checks even if `review.md` was committed after the reviewed implementation ref

#### Scenario: Review artifact commit only
- **WHEN** the only change after `reviewed_ref` is adding or updating `openspec/changes/<name>/review.md`
- **THEN** archive and merge gates MUST NOT block as stale solely because HEAD differs from `reviewed_ref`

#### Scenario: Review is stale
- **WHEN** archive starts and non-review implementation files differ from the implementation state recorded in `review.md`
- **THEN** archive is blocked with instructions to rerun `/review`
