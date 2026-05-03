## 1. Design Skill Guidance

- [x] 1.1 Update `.codex/skills/design/SKILL.md` under specialist analysis execution with Codex-safe spawn guidance.
- [x] 1.2 Document that `fork_context: true` must not be combined with an explicit `agent_type`.
- [x] 1.3 Document the required prompt context for each specialist: workspace path, change name, artifact paths, agent definition path, and output schema path.
- [x] 1.4 Document schema-rejection retry behavior: retry without forked context while keeping specialist output isolated by agent name.

## 2. Spec Alignment

- [x] 2.1 Apply the `design-skill` delta spec for schema-safe managed specialist spawning.
- [x] 2.2 Apply the `design-agent-contracts` delta spec for explicit Codex spawn constraints and retry behavior.
- [x] 2.3 Confirm this change does not modify the agent registry, `DesignAnalysisResult` schema, application code, or runtime tooling.

## 3. Verification

- [x] 3.1 Run OpenSpec validation for `fix-design-agent-spawn-schema`.
- [x] 3.2 Review the final diff to confirm the change is limited to docs/spec/test planning artifacts and `/design` skill documentation.
- [x] 3.3 Confirm `/tdd fix-design-agent-spawn-schema` has enough acceptance criteria to create a scenario for the rejected spawn pattern.
