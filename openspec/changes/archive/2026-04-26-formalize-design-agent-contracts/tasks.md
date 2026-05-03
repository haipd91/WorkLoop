## 1. Contract Artifacts

- [x] 1.1 Add `.codex/schemas/design-analysis-result.schema.json` with required `DesignAnalysisResult` fields and typed array/object structures.
- [x] 1.2 Add `.codex/agents/registry.json` listing all existing design specialist agents with definition path, role, trigger, inputs, output type, parallel group, and failure policy.

## 2. Design Skill Runtime Contract

- [x] 2.1 Update `.codex/skills/design/SKILL.md` to reference the schema and registry as contract sources.
- [x] 2.2 Document core agents, optional agent triggers, parallel execution behavior, serialized fallback, and failure handling in the `/design` skill.
- [x] 2.3 Document how invalid specialist output is repaired once and then treated as failure if still invalid.

## 3. Fixture Coverage

- [x] 3.1 Add a `.codex/fixtures/design/` fixture case with representative input `proposal.md`, `tasks.md`, and spec artifact content.
- [x] 3.2 Add expected fixture output documenting the required `design.md` sections produced from the input artifacts.
- [x] 3.3 Keep fixture expectations section-focused rather than exact full-text snapshots.

## 4. Verification

- [x] 4.1 Review schema, registry, design skill docs, and fixture for internal consistency.
- [x] 4.2 Confirm no runner implementation, runtime validation code, OpenSpec CLI changes, or new dependencies were added.
