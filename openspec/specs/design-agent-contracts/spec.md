# design-agent-contracts Specification

## Purpose
Define the contract artifacts that make `/design` specialist-agent orchestration reviewable and extensible, including specialist output schema, agent registry metadata, runtime behavior, and design synthesis fixtures.

## Requirements
### Requirement: Define DesignAnalysisResult schema
The system SHALL define a standalone schema for `DesignAnalysisResult` that documents the required output shape for `/design` specialist agents.

#### Scenario: Schema lists required fields
- **WHEN** a reviewer opens the `DesignAnalysisResult` schema
- **THEN** the schema lists `scope_analyzed`, `findings`, `impacted_areas`, `assumptions`, `risks`, `unresolved_questions`, and `recommended_decisions` as required fields

#### Scenario: Schema supports future validation
- **WHEN** a future runner needs to validate specialist output
- **THEN** the schema provides machine-readable field names and value shapes without requiring new contract interpretation

### Requirement: Register design specialist agents
The system SHALL define an agent registry that lists every `/design` specialist agent and its contract metadata.

#### Scenario: Registry lists existing agents
- **WHEN** a reviewer opens the design agent registry
- **THEN** the registry lists Architecture, Impact, Data Flow, Failure Modes, Review and Docs, Security, and Performance

#### Scenario: Registry describes agent contract
- **WHEN** a reviewer inspects an agent registry entry
- **THEN** the entry identifies the agent name, role, trigger, definition path, required inputs, optional inputs, output type, parallel group, and failure policy

### Requirement: Document runtime contract for design orchestration
The system SHALL document how `/design` selects, executes, and handles failures from specialist agents.

#### Scenario: Core agents are defined
- **WHEN** `/design` documentation describes the runtime contract
- **THEN** it identifies Architecture, Impact, and Failure Modes as core agents for most design runs

#### Scenario: Optional agents are trigger-driven
- **WHEN** `/design` documentation describes optional agents
- **THEN** it identifies the conditions that trigger Data Flow, Review and Docs, Security, and Performance

#### Scenario: Parallel execution behavior is explicit
- **WHEN** `/design` documentation describes agent execution
- **THEN** it states that independent selected agents should run in parallel when possible and may run serially when parallel execution is unavailable while preserving isolated outputs

#### Scenario: Failure handling is explicit
- **WHEN** a selected specialist agent fails or returns invalid output
- **THEN** the runtime contract defines whether the failure blocks approval, can be recorded as a risk or open question, or requires a repair attempt

#### Scenario: Codex spawn schema constraints are explicit
- **WHEN** `/design` documentation describes specialist-agent execution in Codex
- **THEN** the runtime contract states that `fork_context: true` MUST NOT be combined with an explicit `agent_type`
- **AND** it states that specialist agents receive workspace, change, artifact, agent definition, and schema paths in the prompt and read their own context from disk

#### Scenario: Schema-safe retry behavior is explicit
- **WHEN** `/design` documentation describes recovery from a Codex spawn schema rejection
- **THEN** the runtime contract states that the orchestrator retries without forked context while preserving isolated specialist outputs

### Requirement: Provide design synthesis fixture
The system SHALL provide a fixture that shows how `/design` input artifacts map to expected `design.md` sections.

#### Scenario: Fixture includes input artifacts
- **WHEN** a reviewer opens the design fixture
- **THEN** the fixture includes representative input artifacts for proposal, tasks, and specs

#### Scenario: Fixture includes expected design sections
- **WHEN** a reviewer opens the design fixture expected output
- **THEN** it identifies the expected `design.md` sections that the orchestrator should produce from the input artifacts

### Requirement: Keep implementation out of scope
The system SHALL keep this change limited to architecture contract artifacts and documentation, without implementing runtime enforcement.

#### Scenario: No runner implementation is added
- **WHEN** this change is applied
- **THEN** it does not add code that automatically loads the registry, spawns agents, or validates specialist output at runtime

#### Scenario: No new dependency is required
- **WHEN** this change is applied
- **THEN** it does not require a new package or external runtime dependency
