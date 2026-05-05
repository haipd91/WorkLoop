## ADDED Requirements

### Requirement: Issue acceptance criteria closure
WorkLoop SHALL verify linked GitHub issue acceptance criteria before merging or closing a linked issue for a GitHub-backed change.

#### Scenario: Criteria verified before merge and close
- **WHEN** a GitHub-backed change is ready for merge and has a linked issue with parseable Acceptance Criteria checkboxes
- **THEN** the workflow MUST verify each acceptance criterion against durable evidence before merging the pull request or closing the linked issue

#### Scenario: Criteria evidence is reflected in issue state
- **WHEN** an acceptance criterion is verified by durable evidence
- **THEN** the workflow MUST update that criterion to checked state in the GitHub issue body before merge or close proceeds

#### Scenario: Checked criteria still require evidence
- **WHEN** a linked issue has an acceptance criterion already checked before merge-time verification
- **THEN** the workflow MUST still map that criterion to durable evidence or an explicit documented bypass before merge or close proceeds

#### Scenario: Uncovered criteria block merge and close
- **WHEN** a linked issue has an acceptance criterion without sufficient durable evidence
- **THEN** the workflow MUST block merge and issue close unless the human explicitly accepts and durably documents a bypass

#### Scenario: Missing or ambiguous criteria block merge and close
- **WHEN** the linked issue acceptance criteria section is missing, duplicated, unparseable, or unsafe to edit
- **THEN** the workflow MUST block merge and issue close unless the human explicitly accepts and durably documents a bypass

#### Scenario: Acceptance criteria closure is not deep review
- **WHEN** the workflow verifies issue acceptance criteria before close
- **THEN** the workflow MUST treat that verification as final closure evidence and MUST NOT replace the deep implementation review performed by `/review`
