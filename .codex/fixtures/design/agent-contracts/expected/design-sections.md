# Expected Design Sections: Agent Contracts Fixture

This fixture checks required section coverage and synthesis intent. It is not a full-text snapshot.

## Required Sections

- `## Context`
- `## Goals / Non-Goals`
- `## Proposed Architecture`
- `## Impact Map`
- `## Decisions`
- `## Risks / Trade-offs`
- `## Open Questions`
- `## Review Notes`

## Expected Synthesis

### Context

- Mentions that `/design` already uses specialist agents.
- States that existing contracts are implicit and need formalization.

### Goals / Non-Goals

- Includes schema, registry, runtime contract, and fixture as goals.
- Excludes runner implementation, runtime validation enforcement, OpenSpec CLI changes, and new dependencies.

### Proposed Architecture

- Describes `.codex/schemas/design-analysis-result.schema.json` as the specialist output contract.
- Describes `.codex/agents/registry.json` as the discovery and contract index.
- Describes `.codex/fixtures/design/` as contract fixture coverage.
- States that `.codex/skills/design/SKILL.md` remains the orchestrator workflow.

### Impact Map

- Lists `.codex/schemas/`, `.codex/agents/registry.json`, `.codex/fixtures/design/`, and `.codex/skills/design/SKILL.md`.

### Decisions

- Records JSON Schema as the schema format.
- Records JSON as the registry format.
- Records fixture expectations as section-focused, not exact full-text snapshots.
- Records core agent failure as blocking and optional agent failure as recorded risk/open question.

### Risks / Trade-offs

- Mentions contract drift between registry and agent markdown.
- Mentions that documented validation is not runtime enforcement.

### Open Questions

- Captures future validation and runner implementation as follow-up work when still unresolved.

### Review Notes

- Identifies that this is a contract/documentation change.
- Notes that no runtime runner or validator is added.
