## ADDED Requirements

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
