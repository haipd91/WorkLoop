## Purpose

Define how `/propose` discovers existing OpenSpec context before generating planning artifacts.

## Requirements

### Requirement: Discover existing spec context before proposal generation
The `/propose` workflow SHALL discover existing OpenSpec context before creating or updating planning artifacts for a change.

#### Scenario: Active specs are searched before capability classification
- **WHEN** `/propose <change-name>` runs with a user request or `brief.md`
- **THEN** the workflow MUST extract relevant domain, action, entity, surface, and likely capability terms
- **AND** the workflow MUST search `openspec/specs/**` for matching active specs before deciding whether capabilities are new or modified

#### Scenario: Matched active specs are read
- **WHEN** the active spec search finds matching spec files
- **THEN** `/propose` MUST read the matched spec files before creating `proposal.md` or delta specs
- **AND** `/propose` MUST use those specs to decide whether a delta should use `ADDED`, `MODIFIED`, `REMOVED`, or `RENAMED`

### Requirement: Detect overlapping active changes
The `/propose` workflow SHALL check active OpenSpec changes for overlap before creating planning artifacts.

#### Scenario: Active changes are searched without archive noise
- **WHEN** `/propose <change-name>` runs
- **THEN** the workflow MUST search `openspec/changes/**` for related active change artifacts
- **AND** the workflow MUST exclude `openspec/changes/archive/**` from the default active-change search

#### Scenario: Ambiguous overlap requires clarification
- **WHEN** `/propose` finds another active change with overlapping domain, capability, or requirements
- **THEN** `/propose` MUST ask whether to continue the existing change, merge scope, or proceed with the new change
- **AND** `/propose` MUST NOT silently create conflicting planning artifacts

### Requirement: Record discovered context in planning artifacts
The `/propose` workflow SHALL record concise context-discovery evidence in generated planning artifacts.

#### Scenario: Related context is documented
- **WHEN** `/propose` creates or updates planning artifacts
- **THEN** the generated artifacts MUST identify related active specs that were reviewed
- **AND** the generated artifacts MUST identify related active changes or state that none were found

#### Scenario: Context affects capability decisions
- **WHEN** discovered context causes `/propose` to choose an existing capability instead of a new capability
- **THEN** `proposal.md` MUST list that capability under modified capabilities
- **AND** the related delta spec MUST use the existing capability folder name from `openspec/specs/<capability>/`

### Requirement: Limit archive search to rationale gaps
The `/propose` workflow SHALL avoid searching archived changes by default.

#### Scenario: Archive search is skipped for sufficient active context
- **WHEN** active specs and active changes provide enough context to classify the change
- **THEN** `/propose` MUST NOT search `openspec/changes/archive/**` as part of default context discovery

#### Scenario: Archive search is allowed for conflict or rationale
- **WHEN** active context is insufficient, conflicting, or missing rationale needed for a safe proposal
- **THEN** `/propose` MAY search archived changes
- **AND** `/propose` MUST record why archive context was needed if it influenced generated artifacts
