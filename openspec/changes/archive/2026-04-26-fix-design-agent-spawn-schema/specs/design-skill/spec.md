## MODIFIED Requirements

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
