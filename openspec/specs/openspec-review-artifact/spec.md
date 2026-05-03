## Purpose
Define review as a first-class OpenSpec artifact in the spec-driven-custom workflow.

## Requirements
### Requirement: Model review as an OpenSpec artifact
The `spec-driven-custom` schema SHALL include `review` as a first-class artifact that generates `review.md`.

#### Scenario: Status includes review artifact
- **WHEN** `openspec status --change <name>` runs for a spec-driven-custom change
- **THEN** the returned artifact list includes `review` with output path `review.md`

#### Scenario: Review artifact is missing
- **WHEN** `review.md` does not exist
- **THEN** status reports the `review` artifact as not done

#### Scenario: Review artifact exists
- **WHEN** `review.md` exists
- **THEN** status can report the `review` artifact as done

#### Scenario: Review artifact done is not verdict pass
- **WHEN** status reports the `review` artifact as done
- **THEN** the status means `review.md` exists
- **THEN** the status does not imply the review verdict is pass

### Requirement: Gate review readiness on prior artifacts
The `review` artifact SHALL be blocked until proposal, specs, design, tasks, and tests are done.

#### Scenario: Prior artifacts are incomplete
- **WHEN** one or more required prior artifacts are missing
- **THEN** status reports `review` as blocked with the missing dependencies

#### Scenario: Prior artifacts are complete
- **WHEN** proposal, specs, design, tasks, and tests are done
- **THEN** status reports `review` as ready when `review.md` is absent

### Requirement: Provide review instructions from schema
OpenSpec SHALL provide review instructions for `openspec instructions review --change <name> --json`.

#### Scenario: Review instructions requested
- **WHEN** `openspec instructions review --change <name> --json` runs
- **THEN** the response includes output path `review.md`
- **THEN** the response includes dependencies and context files for proposal, specs, design, tasks, and tests
- **THEN** the response includes instruction text describing review artifact generation

#### Scenario: Generic artifact dispatch is required
- **WHEN** a new schema artifact is added
- **THEN** `openspec instructions <artifact-id> --change <name> --json` resolves from schema artifact metadata without artifact-specific branching

### Requirement: Keep review out of apply prerequisites
The apply phase SHALL continue to require specs, tasks, and tests, but SHALL NOT require review.

#### Scenario: Apply readiness checked before review exists
- **WHEN** specs, tasks, and tests are complete and `review.md` is missing
- **THEN** apply remains ready or all_done according to task state
- **THEN** review remains the next post-apply gate before archive
