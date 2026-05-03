## MODIFIED Requirements

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
