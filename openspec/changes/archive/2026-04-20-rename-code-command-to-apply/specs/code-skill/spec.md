## MODIFIED Requirements

### Requirement: Read tests directory as implementation context
The system SHALL check for `openspec/changes/<name>/tests/` before starting implementation through `/apply` and use its contents as context to guide each task's implementation.

#### Scenario: Tests directory exists
- **WHEN** `/apply` starts and `openspec/changes/<name>/tests/` exists
- **THEN** the skill reads all test files and uses them as implementation context for each task

#### Scenario: Tests directory does not exist
- **WHEN** `/apply` starts and no `tests/` directory is found
- **THEN** the skill proceeds with implementation using only proposal, design, and tasks artifacts
