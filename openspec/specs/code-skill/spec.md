## Purpose
Define how `/apply` gathers implementation context before working through a change's tasks.
## Requirements
### Requirement: Read tests directory as implementation context
The system SHALL check for `openspec/changes/<name>/tests/` before starting implementation through `/apply` and use its contents as context to guide each task's implementation.

#### Scenario: Tests directory exists
- **WHEN** `/apply` starts and `openspec/changes/<name>/tests/` exists
- **THEN** the skill reads all test files and uses them as implementation context for each task

#### Scenario: Tests directory does not exist
- **WHEN** `/apply` starts and no `tests/` directory is found
- **THEN** the skill proceeds with implementation using only proposal, design, and tasks artifacts

### Requirement: Hand off to /review after implementation
The `/apply` skill SHALL direct users to run `/review` after implementation completes instead of implying the change is ready to archive.

#### Scenario: Apply completes implementation
- **WHEN** `/apply` finishes all implementation tasks for a change
- **THEN** the final handoff names `/review` as the next workflow step before archive

#### Scenario: Apply instructions report all done
- **WHEN** `openspec instructions apply --change <name> --json` reports all implementation tasks complete
- **THEN** the instruction text routes users to `/review <name>` instead of archive

#### Scenario: Apply cannot complete implementation
- **WHEN** `/apply` stops with unfinished tasks or unresolved implementation blockers
- **THEN** the final handoff does not suggest archive and records what remains before `/review` can run
