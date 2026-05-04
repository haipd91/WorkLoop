## Why

`/propose` needs to create planning artifacts from repository truth, not just the current request or chat context. Today it can miss existing specs for the same domain, screen, action, or capability, which can cause duplicate requirements, incorrect `ADDED` vs `MODIFIED` delta choices, or conflicts with active changes.

## What Changes

- Add a Spec Context Discovery step to `/propose` before it generates proposal, specs, design, and tasks.
- Require `/propose` to extract lightweight search terms from the user request and optional `brief.md`.
- Require `/propose` to search active specs and active changes before capability classification.
- Require matched specs and active change artifacts to be read before deciding new vs modified capabilities and delta operations.
- Require concise context evidence in generated planning artifacts.
- Keep archive search optional and limited to conflict, missing rationale, or insufficient active context cases.

## Existing Context Reviewed

- `openspec/specs/github-workflow/spec.md`
  - Relevant context: WorkLoop artifacts must provide durable context without relying on chat history.
  - Impact: This change strengthens pre-proposal artifact grounding but does not change GitHub delivery flow.
- `openspec/specs/code-skill/spec.md`
  - Relevant context: `/apply` already has explicit implementation context loading.
  - Impact: `/propose` needs a similar explicit context-discovery contract for planning.
- Active changes
  - `issue-7-spec-context-discovery-propose` is the only active matching change found.

## Capabilities

### New Capabilities

- `propose-skill`: Defines how `/propose` discovers existing OpenSpec context before generating planning artifacts.

### Modified Capabilities

- None.

## Impact

- `.codex/skills/propose/SKILL.md`
- `openspec/schemas/spec-driven-custom/schema.yaml`
- `openspec/schemas/spec-driven-custom/templates/proposal.md`
- New active spec capability: `openspec/specs/propose-skill/spec.md` after archive sync
