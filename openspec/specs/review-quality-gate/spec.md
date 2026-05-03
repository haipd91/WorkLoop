## Purpose
Define the `/review` quality gate between implementation and archive.

## Requirements
### Requirement: Use OpenSpec review artifact state in archive flow
Archive SHALL treat the OpenSpec `review` artifact state as part of its review gate preflight.

#### Scenario: Review artifact is not done
- **WHEN** archive starts and OpenSpec status reports `review` as missing, blocked, or not done
- **THEN** archive is blocked with instructions to run `/review`

#### Scenario: Review artifact is done
- **WHEN** archive starts and OpenSpec status reports `review` as done
- **THEN** archive continues to parse `review.md` metadata for verdict, blocking findings, and stale state

#### Scenario: Review graph state is unavailable
- **WHEN** archive starts and OpenSpec status cannot expose review artifact state
- **THEN** archive uses the existing direct `review.md` metadata gate as a documented bridge

### Requirement: Require post-apply review artifact
The workflow SHALL require `/review` to produce a review artifact for an applied change before the change can be archived.

#### Scenario: Applied change has review artifact
- **WHEN** archive starts for a change after implementation
- **THEN** the workflow checks for a review artifact in the change directory

#### Scenario: Review artifact is missing
- **WHEN** archive starts for an applied change and no review artifact exists
- **THEN** archive is blocked with instructions to run `/review` first

### Requirement: Preserve phase 1 review metadata gate
Archive SHALL continue to enforce the phase 1 `review.md` metadata gate after review becomes graph-aware.

#### Scenario: Graph review artifact exists but metadata fails
- **WHEN** status reports `review` as done but `review.md` metadata is malformed, stale, non-pass, or blocking
- **THEN** archive is blocked

#### Scenario: Graph review artifact exists and metadata passes
- **WHEN** status reports `review` as done and `review.md` metadata passes
- **THEN** archive may continue to remaining archive checks

### Requirement: Define parseable review artifact contract
The review artifact SHALL include deterministic machine-checkable metadata and required human-readable sections.

#### Scenario: Review artifact is valid
- **WHEN** `/review` writes `openspec/changes/<name>/review.md`
- **THEN** the artifact includes `verdict`, `blocking_findings`, `reviewed_ref`, `reviewed_diff`, and `reviewed_at` metadata
- **THEN** the artifact includes Findings, Tests Run, Residual Risk, and Verdict sections

#### Scenario: Review artifact is malformed
- **WHEN** archive starts and `review.md` is missing required metadata, has duplicate or contradictory metadata, or uses unknown values
- **THEN** archive is blocked with the malformed review condition as the reason

### Requirement: Capture review outcome
The review artifact SHALL record findings, tests run, residual risk, and verdict.

#### Scenario: Review completes
- **WHEN** `/review` finishes for an applied change
- **THEN** the review artifact includes findings, tests run, residual risk, and verdict

#### Scenario: No findings exist
- **WHEN** `/review` finds no issues
- **THEN** the review artifact records that no findings were found and still records tests run, residual risk, and verdict

### Requirement: Block archive on failing review
Archive SHALL be blocked when the review verdict is not pass or blocking findings remain.

#### Scenario: Review verdict is pass
- **WHEN** archive starts and the review artifact has verdict pass with no blocking findings
- **THEN** archive may continue to the remaining archive checks

#### Scenario: Review verdict is not pass
- **WHEN** archive starts and the review artifact verdict is not pass
- **THEN** archive is blocked with the review result as the reason

#### Scenario: Blocking findings remain
- **WHEN** archive starts and the review artifact reports unresolved blocking findings
- **THEN** archive is blocked until the findings are resolved and `/review` passes

### Requirement: Block archive on stale review
Archive SHALL be blocked when the implementation state differs from the state recorded in the review artifact.

#### Scenario: Review matches current state
- **WHEN** archive starts and the current implementation state matches `reviewed_ref` and `reviewed_diff`
- **THEN** archive may continue to the remaining archive checks

#### Scenario: Review is stale
- **WHEN** archive starts and the current implementation state differs from `reviewed_ref` or `reviewed_diff`
- **THEN** archive is blocked with instructions to rerun `/review`

### Requirement: Review implementation against approved artifacts
`/review` SHALL compare the implementation against the change proposal, specs, design, tasks, and tests when those artifacts exist.

#### Scenario: All planning artifacts exist
- **WHEN** `/review` starts for a change with proposal, specs, design, tasks, and tests
- **THEN** `/review` uses those artifacts as source-of-truth context for scope and correctness

#### Scenario: Some planning artifacts are missing
- **WHEN** `/review` starts and optional planning artifacts are missing
- **THEN** `/review` uses the available artifacts and records missing context as residual risk

### Requirement: Preserve human archive acceptance
Archive SHALL present review verdict and residual risk before final archive action so the human owns final risk acceptance.

#### Scenario: Review passes before archive
- **WHEN** archive starts and review gate checks pass
- **THEN** archive presents the review verdict and residual risk before finalizing the change

### Requirement: Allow only explicit documented legacy bypass
Archive SHALL allow bypass for pre-gate legacy changes only when the bypass is documented in the review artifact.

#### Scenario: Legacy bypass is documented
- **WHEN** archive starts for a pre-gate legacy change and `review.md` documents the bypass reason with passing metadata
- **THEN** archive may continue to the remaining archive checks

#### Scenario: Legacy bypass is missing
- **WHEN** archive starts for a pre-gate legacy change and no valid review or documented bypass exists
- **THEN** archive is blocked with instructions to run `/review` or document the bypass
