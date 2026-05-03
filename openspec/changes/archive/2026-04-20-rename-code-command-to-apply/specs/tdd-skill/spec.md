## MODIFIED Requirements

### Requirement: Human review and approve before handoff
The system SHALL present generated test files to human for review and require explicit approval before the workflow can proceed to `/apply`.

#### Scenario: Human approves test files
- **WHEN** human reviews generated tests and approves
- **THEN** skill confirms save and suggests running `/apply`

#### Scenario: Human requests changes
- **WHEN** human reviews and requests modifications to test files
- **THEN** skill updates the relevant test files and presents for re-review

### Requirement: Provide scenario-based test fallback
The system SHALL generate structured scenario-based test files when runnable test generation is not possible because no framework can be detected.

#### Scenario: Scenario files capture behavioral intent
- **WHEN** `/tdd` falls back to scenario mode
- **THEN** each generated file records source task, assumptions, happy path, edge cases, error cases, and expected outcomes so implementation can use it as behavior guidance

#### Scenario: Scenario files remain compatible with `/apply`
- **WHEN** scenario-based test files are generated under `openspec/changes/<name>/tests/`
- **THEN** they remain available for `/apply` to read as implementation context without requiring a separate handoff format
