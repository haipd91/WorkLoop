## MODIFIED Requirements

### Requirement: Hand off to /review after implementation
The `/apply` skill and OpenSpec apply instructions SHALL direct users to run `/review` after implementation completes instead of implying the change is ready to archive.

#### Scenario: Apply completes implementation
- **WHEN** `/apply` finishes all implementation tasks for a change
- **THEN** the final handoff names `/review` as the next workflow step before archive

#### Scenario: Apply instructions report all done
- **WHEN** `openspec instructions apply --change <name> --json` reports all implementation tasks complete
- **THEN** the instruction text routes users to `/review <name>` instead of archive

#### Scenario: Apply cannot complete implementation
- **WHEN** `/apply` stops with unfinished tasks or unresolved implementation blockers
- **THEN** the final handoff does not suggest archive and records what remains before `/review` can run
