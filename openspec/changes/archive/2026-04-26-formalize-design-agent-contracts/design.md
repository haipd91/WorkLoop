## Context

The current `.codex` design workflow already defines a manager-controlled `/design` orchestrator and seven specialist agent definition files. The workflow depends on implicit contracts embedded in markdown:

- Specialist agent output shape is listed in `.codex/skills/design/SKILL.md`, but no standalone schema exists.
- Agent selection rules are described in prose, but no registry lists agent roles, triggers, inputs, and outputs in one place.
- Runtime behavior for parallel execution, optional agents, invalid output, and failed agents is not captured as a reusable contract.
- No fixture shows how input artifacts should map to expected `design.md` sections.

This change formalizes those contracts while keeping runner implementation and validation enforcement out of scope.

## Goals / Non-Goals

**Goals:**

- Add a standalone `DesignAnalysisResult` schema contract.
- Add a design agent registry that references all existing specialist agents.
- Add fixture artifacts that document expected `/design` synthesis behavior.
- Update `/design` skill documentation to reference the schema, registry, fixture pattern, and runtime contract.
- Keep the contract readable and reviewable by humans while leaving room for future machine validation.

**Non-Goals:**

- Implement an automatic registry loader.
- Enforce the schema at runtime with validator code.
- Change OpenSpec CLI behavior.
- Add real agent spawning or process management beyond existing instruction-level workflow.
- Add new dependencies.

## Decisions

### Decision: Use JSON Schema for `DesignAnalysisResult`

Use `.codex/schemas/design-analysis-result.schema.json` as the source of truth for specialist output shape.

Rationale:
- JSON Schema is standard and machine-validatable later.
- It can document required fields without adding runtime code now.
- It avoids inventing a custom schema format.

Alternative considered: markdown-only schema. Rejected because it remains ambiguous and is harder to validate later.

### Decision: Use a JSON agent registry

Use `.codex/agents/registry.json` to list agent definitions, roles, triggers, input artifact requirements, output type, and execution metadata.

Rationale:
- JSON is simple, dependency-free, and easy to validate later.
- The registry can remain compact while pointing to the richer `agents/*.md` files.
- It avoids duplicating complete agent instructions.

Alternative considered: YAML registry. Rejected for now because JSON has fewer parser assumptions and aligns better with JSON Schema.

### Decision: Keep registry as index and contract, not instruction source

The registry should not replace agent definition markdown. It should reference each agent file and summarize:

- `name`
- `definition`
- `role`
- `trigger`
- `requiredInputs`
- `optionalInputs`
- `outputType`
- `parallelGroup`
- `failurePolicy`

Rationale:
- Agent instructions stay readable in existing markdown files.
- Registry gives orchestrator and future tooling a stable discovery layer.
- Duplication stays low.

### Decision: Use fixture documentation, not runnable test code

Add a fixture under `.codex/fixtures/design/` with input artifacts and expected design sections.

Rationale:
- The current change is contract formalization, not runner implementation.
- A fixture documents expected behavior without requiring test framework integration.
- Future validator or runner work can reuse the same fixture.

### Decision: Core agent failures block approval; optional failures are recorded

Runtime contract should define:

- Core agents: `Architecture`, `Impact`, `Failure Modes`.
- Optional agents: `Data Flow`, `Review and Docs`, `Security`, `Performance`.
- Selected agents are independent and should run in parallel when possible.
- If parallel execution is unavailable, serialized execution is acceptable if outputs remain isolated.
- Core agent failure blocks final design approval until retried or explicitly recorded as unresolved.
- Optional agent failure does not block by default, but must be recorded in `Risks / Trade-offs` or `Open Questions`.
- Invalid agent output should be repaired once; repeated invalid output is treated as agent failure.

Rationale:
- Core agents define the minimum design review surface.
- Optional agents should not create unnecessary workflow friction.
- Failure behavior must be explicit so the orchestrator does not silently hide missing analysis.

## Risks / Trade-offs

- Contract files may drift from agent markdown -> Mitigation: registry references exact agent definition paths and `/design` docs must state both are part of the same contract.
- JSON registry is less pleasant to edit than YAML -> Mitigation: keep entries compact and avoid embedding long instructions.
- Fixture may be mistaken for an exhaustive test suite -> Mitigation: name it as a contract fixture and keep expected output focused on required sections, not exact full text.
- Runtime contract may imply enforcement that does not exist yet -> Mitigation: explicitly state validation and runner enforcement are out of scope for this change.

## Migration Plan

1. Add schema and registry files under `.codex`.
2. Add one design fixture case under `.codex/fixtures/design/`.
3. Update `.codex/skills/design/SKILL.md` to reference the new contracts.
4. Verify the artifacts are readable and internally consistent.

Rollback is file-level removal of the added contract artifacts and reversal of the design skill documentation update.

## Open Questions

- Should future work archive `design-agent-contracts` into the existing `design-skill` spec or keep it as a separate capability?
- Should future runtime validation use JSON Schema directly or wrap it in OpenSpec-specific validation commands?
