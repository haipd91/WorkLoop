# design-skill Specification

## Purpose
Define how `/design` turns change artifacts into a reviewable technical design handoff,
using a manager-controlled orchestrator and specialist agents with explicit contracts.

## Requirements
### Requirement: Generate design artifact from change artifacts
The system SHALL provide a `/design` skill that reads the current change artifacts
and produces `openspec/changes/<name>/design.md` as a technical design artifact for
implementation handoff.

#### Scenario: Generate design from available artifacts
- **WHEN** user runs `/design` for a change that has `proposal.md` and `tasks.md`
- **THEN** the skill reads those artifacts, uses `brief.md` if present, and creates
  a `design.md` file in the same change directory

### Requirement: Analyze impact through orchestrated specialist agents
The system SHALL analyze design impact through an orchestrated set of specialist
agents with clearly separated ownership rather than relying on unspecified
parallel analysis.

#### Scenario: Orchestrator selects specialist agents
- **WHEN** `/design` starts analysis for a change
- **THEN** the orchestrator selects a set of specialist agents based on the
  change context and assigns each agent a distinct analysis responsibility

#### Scenario: Specialist agents avoid overlap
- **WHEN** multiple specialist agents are used in one `/design` run
- **THEN** each agent analyzes only its assigned criteria area and does not
  duplicate another agent's core responsibility unless the orchestrator
  explicitly requests cross-cutting notes

### Requirement: Synthesize a standardized design document
The system SHALL merge specialist-agent outputs into a standardized `design.md`
through a single orchestrator that owns synthesis, conflict resolution, and
final artifact generation.

#### Scenario: Orchestrator owns final synthesis
- **WHEN** specialist agents finish their analysis
- **THEN** only the orchestrator merges their outputs, resolves or records
  conflicts, and writes the final `design.md`

#### Scenario: Conflicting agent outputs are not hidden
- **WHEN** specialist-agent outputs disagree on architectural impact, risks, or
  proposed decisions
- **THEN** the orchestrator uses proposal, brief, and specs as source-of-truth
  inputs and records unresolved disagreements in the final design instead of
  silently dropping them

### Requirement: Support human review and iterative refinement
The system SHALL present the generated design for human review, accept feedback,
and update the relevant parts of the design before treating the artifact as ready.

#### Scenario: Human requests design changes
- **WHEN** human reviews the generated design and provides feedback
- **THEN** the skill revises the affected sections and presents the updated design
  for another review round

### Requirement: Preserve approved design as `/apply` input
The system SHALL keep the approved `design.md` in the change directory so `/apply`
can use it as implementation context without requiring the human to restate intent.

#### Scenario: Approved design is handed off to implementation
- **WHEN** human approves the generated design
- **THEN** `design.md` remains stored in the change directory and is ready to be
  consumed by `/apply` as part of the implementation context

### Requirement: Define specialist agent contracts
The system SHALL define a fixed contract for each specialist agent so multi-agent
design analysis remains traceable, reviewable, and consistent across runs.

#### Scenario: Specialist agent contract is explicit
- **WHEN** a specialist agent is invoked by `/design`
- **THEN** the agent receives a clearly scoped responsibility and returns
  structured output including findings, impacted areas, assumptions, risks,
  unresolved questions, and recommended decisions

#### Scenario: Optional agents are context-driven
- **WHEN** a change does not require all available specialist perspectives
- **THEN** the orchestrator activates only the agents needed for that change
  instead of always running the full agent set

### Requirement: Use manager-controlled collaboration by default
The system SHALL use a manager-controlled collaboration pattern by default for
`/design`, where specialist agents support the orchestrator rather than taking
over ownership of the final response.

#### Scenario: Agent-as-tool is the default collaboration pattern
- **WHEN** `/design` needs multiple specialist analyses for one artifact
- **THEN** the orchestrator calls specialist agents as managed sub-agents or
  tools and retains ownership of the final response and artifact

#### Scenario: Handoff is reserved for delegated ownership
- **WHEN** a future `/design` workflow branch requires a specialist to own the
  next response instead of contributing analysis only
- **THEN** that branch may use handoff explicitly, but handoff is not treated as
  the default pattern for design synthesis

#### Scenario: Specialist spawn uses schema-safe Codex calls
- **WHEN** `/design` dispatches a specialist agent in Codex
- **THEN** the orchestrator MUST NOT combine `fork_context: true` with an
  explicit `agent_type`
- **AND** the orchestrator passes the workspace path, change name, artifact
  paths, specialist definition path, and output schema path in the prompt so the
  specialist can read its own context from disk

#### Scenario: Spawn schema rejection is retried without forked context
- **WHEN** a specialist spawn is rejected because the spawn request shape is not
  accepted by Codex
- **THEN** the orchestrator retries the specialist analysis without forked
  context and keeps that specialist output isolated by agent name
