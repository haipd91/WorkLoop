# Test Scenarios: Design Agent Contracts

- Source tasks: 1.1-4.2
- Related requirements:
  - Define DesignAnalysisResult schema
  - Register design specialist agents
  - Document runtime contract for design orchestration
  - Provide design synthesis fixture
  - Keep implementation out of scope
- Assumptions:
  - Tests are scenario-based because this change formalizes `.codex` contracts and documentation rather than runtime code.
  - Future runner work may convert these scenarios into executable validation.

## Happy Path

- Given `.codex/schemas/design-analysis-result.schema.json` exists
- When a reviewer inspects it
- Then it defines all required `DesignAnalysisResult` fields:
  - `scope_analyzed`
  - `findings`
  - `impacted_areas`
  - `assumptions`
  - `risks`
  - `unresolved_questions`
  - `recommended_decisions`

- Given `.codex/agents/registry.json` exists
- When a reviewer inspects it
- Then it lists all seven existing design agents and points each entry to the corresponding `.codex/agents/*.md` file

- Given `.codex/skills/design/SKILL.md` is updated
- When a reviewer reads the runtime contract section
- Then it explains core agents, optional agents, parallel execution, serialized fallback, invalid output repair, and failure handling

- Given a `.codex/fixtures/design/` case exists
- When a reviewer opens the fixture
- Then it contains representative input artifacts and expected `design.md` sections

## Edge Cases

- Optional agent not selected:
  - Given a change does not touch security-sensitive behavior
  - When the orchestrator selects agents
  - Then Security does not need to run, and the registry still explains when it should run

- Parallel execution unavailable:
  - Given the environment cannot run selected agents in parallel
  - When `/design` runs serialized specialist passes
  - Then outputs remain isolated by agent and the orchestrator still performs final synthesis

- Fixture expected output:
  - Given fixture expected sections are reviewed
  - When comparing against generated design behavior
  - Then the fixture checks required sections and synthesis intent, not exact full-document text

## Error Cases

- Core agent failure:
  - Given Architecture, Impact, or Failure Modes fails
  - When `/design` reaches synthesis
  - Then final design approval is blocked or the unresolved failure is explicitly recorded

- Optional agent failure:
  - Given Data Flow, Review and Docs, Security, or Performance fails after being selected
  - When `/design` reaches synthesis
  - Then the failure is recorded under risks or open questions unless the orchestrator decides it blocks the change

- Invalid specialist output:
  - Given a selected agent returns output that does not match `DesignAnalysisResult`
  - When the orchestrator handles the result
  - Then it requests one repair attempt before treating repeated invalid output as failure

## Expected Outcomes

- Contract artifacts make `/design` agent behavior reviewable without implementing a runner.
- The registry and schema reduce ambiguity in future agent additions.
- Runtime contract documents what happens when execution is parallel, serialized, partial, or failed.
- The change does not add runtime validation code, OpenSpec CLI changes, or new dependencies.

## Notes for Implementation

- Keep tests scenario-based unless a runnable validator already exists.
- Do not turn fixture expectations into brittle full-text snapshots.
- Treat this file as implementation guidance for `/apply`, not as an executable test suite.
