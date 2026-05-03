# tdd-skill Specification

## Purpose
Define how `/tdd` generates pre-implementation test artifacts from change context,
using runnable test code when a project framework is clear and structured scenario
files when it is not.
## Requirements
### Requirement: Generate test files from artifacts
The system SHALL read all available artifacts for a change (proposal.md, specs/, design.md, tasks.md) and generate test files covering all identifiable cases into `openspec/changes/<name>/tests/`.

#### Scenario: Generate tests from complete artifact set
- **WHEN** user runs `/tdd` on a change with proposal, specs, design, and tasks present
- **THEN** skill generates test files in `openspec/changes/<name>/tests/` covering all cases from all artifacts

#### Scenario: Generate tests from partial artifact set
- **WHEN** user runs `/tdd` on a change with only proposal and tasks present
- **THEN** skill generates test files using available artifacts without failing

### Requirement: Auto-detect test framework
The system SHALL detect the project's test framework from project files when possible and choose the most appropriate test output mode without requiring manual configuration in common cases.

#### Scenario: Framework detected from project files
- **WHEN** project has a recognizable test framework configuration
- **THEN** generated tests use correct syntax and conventions for that framework

#### Scenario: Framework cannot be detected
- **WHEN** no test framework can be detected from project files
- **THEN** the skill generates scenario-based test files in `openspec/changes/<name>/tests/` instead of blocking on framework selection

#### Scenario: Framework detection is ambiguous
- **WHEN** project signals multiple plausible test frameworks and the correct one cannot be inferred confidently
- **THEN** the skill asks human to specify which framework should be used before generating runnable test code

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
