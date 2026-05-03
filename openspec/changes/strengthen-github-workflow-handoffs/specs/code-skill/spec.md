## MODIFIED Requirements

### Requirement: Hand off to /review after implementation
The `/apply` skill SHALL direct users to publish or update GitHub PR evidence before `/review` when the change is GitHub-backed, and SHALL NOT imply the change is ready to archive.

#### Scenario: Apply completes GitHub-backed implementation
- **WHEN** `/apply` finishes all implementation tasks for a change that has or requires GitHub issue tracking
- **THEN** the final handoff names `/github:create-pr <change-name>` as the next workflow step before `/review`

#### Scenario: Apply completes explicit local-only implementation
- **WHEN** `/apply` finishes all implementation tasks for a change where the human explicitly accepted local-only work
- **THEN** the final handoff names `/review <change-name>` as the next workflow step before archive
- **THEN** the handoff records that GitHub issue and PR evidence were intentionally skipped

#### Scenario: Apply instructions report all done
- **WHEN** `openspec instructions apply --change <name> --json` reports all implementation tasks complete
- **THEN** the instruction text routes GitHub-backed changes to `/github:create-pr <name>` instead of `/review <name>` or archive

#### Scenario: Apply cannot complete implementation
- **WHEN** `/apply` stops with unfinished tasks or unresolved implementation blockers
- **THEN** the final handoff does not suggest archive and records what remains before GitHub PR creation or `/review` can run
