## MODIFIED Requirements

### Requirement: Preserve approved design as `/apply` input
The system SHALL keep the approved `design.md` in the change directory so `/apply`
can use it as implementation context without requiring the human to restate intent.

#### Scenario: Approved design is handed off to implementation
- **WHEN** human approves the generated design
- **THEN** `design.md` remains stored in the change directory and is ready to be
  consumed by `/apply` as part of the implementation context
