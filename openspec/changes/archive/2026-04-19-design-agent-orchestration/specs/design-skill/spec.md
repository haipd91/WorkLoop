## MODIFIED Requirements

### Requirement: Analyze impact through grouped parallel criteria
The system SHALL analyze design impact through an orchestrated set of specialist agents with clearly separated ownership rather than relying on unspecified parallel analysis.

#### Scenario: Orchestrator selects specialist agents
- **WHEN** `/design` starts analysis for a change
- **THEN** the orchestrator selects a set of specialist agents based on the change context and assigns each agent a distinct analysis responsibility

#### Scenario: Specialist agents avoid overlap
- **WHEN** multiple specialist agents are used in one `/design` run
- **THEN** each agent analyzes only its assigned criteria area and does not duplicate another agent's core responsibility unless the orchestrator explicitly requests cross-cutting notes

### Requirement: Synthesize a standardized design document
The system SHALL merge specialist-agent outputs into a standardized `design.md` through a single orchestrator that owns synthesis, conflict resolution, and final artifact generation.

#### Scenario: Orchestrator owns final synthesis
- **WHEN** specialist agents finish their analysis
- **THEN** only the orchestrator merges their outputs, resolves or records conflicts, and writes the final `design.md`

#### Scenario: Conflicting agent outputs are not hidden
- **WHEN** specialist-agent outputs disagree on architectural impact, risks, or proposed decisions
- **THEN** the orchestrator uses proposal, brief, and specs as source-of-truth inputs and records unresolved disagreements in the final design instead of silently dropping them

## ADDED Requirements

### Requirement: Define specialist agent contracts
The system SHALL define a fixed contract for each specialist agent so multi-agent design analysis remains traceable, reviewable, and consistent across runs.

#### Scenario: Specialist agent contract is explicit
- **WHEN** a specialist agent is invoked by `/design`
- **THEN** the agent receives a clearly scoped responsibility and returns structured output including findings, impacted areas, assumptions, risks, unresolved questions, and recommended decisions

#### Scenario: Optional agents are context-driven
- **WHEN** a change does not require all available specialist perspectives
- **THEN** the orchestrator activates only the agents needed for that change instead of always running the full agent set

### Requirement: Use manager-controlled collaboration by default
The system SHALL use a manager-controlled collaboration pattern by default for `/design`, where specialist agents support the orchestrator rather than taking over ownership of the final response.

#### Scenario: Agent-as-tool is the default collaboration pattern
- **WHEN** `/design` needs multiple specialist analyses for one artifact
- **THEN** the orchestrator calls specialist agents as managed sub-agents or tools and retains ownership of the final response and artifact

#### Scenario: Handoff is reserved for delegated ownership
- **WHEN** a future `/design` workflow branch requires a specialist to own the next response instead of contributing analysis only
- **THEN** that branch may use handoff explicitly, but handoff is not treated as the default pattern for design synthesis
