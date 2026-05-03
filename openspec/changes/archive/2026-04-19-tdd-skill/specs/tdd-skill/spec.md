## ADDED Requirements

### Requirement: Generate test files from artifacts
The system SHALL read all available artifacts for a change (proposal.md, specs/, design.md, tasks.md) and generate test files covering all identifiable cases into `openspec/changes/<name>/tests/`.

#### Scenario: Generate tests from complete artifact set
- **WHEN** user runs `/tdd` on a change with proposal, specs, design, and tasks present
- **THEN** skill generates test files in `openspec/changes/<name>/tests/` covering all cases from all artifacts

#### Scenario: Generate tests from partial artifact set
- **WHEN** user runs `/tdd` on a change with only proposal and tasks present
- **THEN** skill generates test files using available artifacts without failing

### Requirement: Auto-detect test framework
The system SHALL detect the project's test framework from project files (package.json, pyproject.toml, CLAUDE.md, etc.) and generate tests in the appropriate format without requiring manual configuration.

#### Scenario: Framework detected from project files
- **WHEN** project has a recognizable test framework configuration
- **THEN** generated tests use correct syntax and conventions for that framework

#### Scenario: Framework cannot be detected
- **WHEN** no test framework can be detected from project files
- **THEN** skill asks human to specify the framework before generating

### Requirement: Human review and approve before handoff
The system SHALL present generated test files to human for review and require explicit approval before the workflow can proceed to `/code`.

#### Scenario: Human approves test files
- **WHEN** human reviews generated tests and approves
- **THEN** skill confirms save and suggests running `/code`

#### Scenario: Human requests changes
- **WHEN** human reviews and requests modifications to test files
- **THEN** skill updates the relevant test files and presents for re-review
