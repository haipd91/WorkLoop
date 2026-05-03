## Why

The `/design` workflow already describes an AI-native orchestrator with specialist agents, but its contracts are only implicit in markdown instructions. Formalizing those contracts now makes the workflow easier to review, extend, and verify before any runner or validator implementation exists.

## What Changes

- Add a documented schema contract for `DesignAnalysisResult`.
- Add an agent registry contract that lists design agents, roles, triggers, inputs, and outputs.
- Add fixture coverage showing how `/design` input artifacts map to expected design sections.
- Update `/design` workflow documentation with runtime contract rules for core agents, optional agents, parallel execution, and failure handling.
- Keep this change limited to `.codex` architecture contract artifacts and OpenSpec documentation.

## Capabilities

### New Capabilities

- `design-agent-contracts`: Defines the schema, registry, runtime contract, and fixture expectations for the `/design` specialist-agent workflow.

### Modified Capabilities

- None.

## Impact

- Adds contract artifacts under `.codex`, expected paths:
  - `.codex/schemas/design-analysis-result.schema.json`
  - `.codex/agents/registry.json`
  - `.codex/fixtures/design/...`
- Updates `.codex/skills/design/SKILL.md` to reference the registry, schema, fixture expectations, and runtime contract.
- Does not change application runtime code, OpenSpec CLI behavior, or real agent spawning.
