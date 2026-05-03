## Why

`/design` currently documents specialist-agent orchestration but does not state the Codex-safe `spawn_agent` calling pattern. This leaves the orchestrator likely to combine `fork_context: true` with an explicit `agent_type`, causing the first specialist spawn to be rejected before analysis starts.

## What Changes

- Document the schema-safe specialist spawning pattern for `/design`.
- State that specialist agents should receive workspace, change, artifact, definition, and schema paths in their prompt and read context from disk.
- State that `/design` must not combine `fork_context: true` with an explicit `agent_type`.
- Document the retry/fallback pattern when a spawn fails for schema reasons: retry without forked context and keep specialist outputs isolated.

## Capabilities

### New Capabilities

- None.

### Modified Capabilities

- `design-skill`: Clarify the runtime behavior for spawning managed specialist agents in Codex.
- `design-agent-contracts`: Extend the runtime contract documentation to include schema-safe spawn constraints and fallback behavior.

## Impact

- `.codex/skills/design/SKILL.md`
- `openspec/specs/design-skill/spec.md`
- `openspec/specs/design-agent-contracts/spec.md`
- `openspec/changes/fix-design-agent-spawn-schema/specs/**/*.md`
- No application runtime, dependency, registry, or output schema changes.
