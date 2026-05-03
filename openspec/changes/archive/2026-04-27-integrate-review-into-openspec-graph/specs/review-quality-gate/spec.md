## ADDED Requirements

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

### Requirement: Preserve phase 1 review metadata gate
Archive SHALL continue to enforce the phase 1 `review.md` metadata gate after review becomes graph-aware.

#### Scenario: Graph review artifact exists but metadata fails
- **WHEN** status reports `review` as done but `review.md` metadata is malformed, stale, non-pass, or blocking
- **THEN** archive is blocked

#### Scenario: Graph review artifact exists and metadata passes
- **WHEN** status reports `review` as done and `review.md` metadata passes
- **THEN** archive may continue to remaining archive checks
