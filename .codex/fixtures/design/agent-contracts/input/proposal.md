## Why

The `/design` workflow uses specialist agents, but their contracts are implicit in prose. A formal schema, registry, runtime contract, and fixture are needed before future runner work can rely on the workflow.

## What Changes

- Add a `DesignAnalysisResult` schema.
- Add a registry for `/design` specialist agents.
- Document runtime behavior for core agents, optional agents, parallel execution, and failure handling.
- Add fixture coverage for expected design synthesis sections.

## Capabilities

### New Capabilities

- `design-agent-contracts`: Defines schema, registry, runtime contract, and fixture expectations for `/design` specialist-agent workflow.

### Modified Capabilities

- None.

## Impact

- `.codex/schemas/design-analysis-result.schema.json`
- `.codex/agents/registry.json`
- `.codex/fixtures/design/...`
- `.codex/skills/design/SKILL.md`
