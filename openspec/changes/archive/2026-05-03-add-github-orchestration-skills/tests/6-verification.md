# Test Scenario: Verification

- Source tasks:
  - 6.1 Run OpenSpec validation for the change and fix artifact issues.
  - 6.2 Review skill instructions against the GitHub orchestration spec requirements.
  - 6.3 Review docs against the brief acceptance criteria.
- Related requirements:
  - GitHub orchestration skills
  - MCP GitHub usage
  - Handoff contract
  - Workflow documentation
- Assumptions:
  - Scenario tests are the appropriate mode because no runnable project test framework is detected.

## Happy Path

- When implementation is complete
- Then `openspec validate add-github-orchestration-skills` passes.
- And every required GitHub skill has a `SKILL.md` or equivalent skill instruction file.
- And every skill can be mapped to MCP usage, no-chat-secrets, handoff contract, read-only/mutating boundary, and blocked-state behavior.
- And docs can be mapped back to every brief acceptance criterion.

## Edge Cases

- When an open design question remains about merge strategy or commit behavior
- Then it is documented as a follow-up or human choice and does not invalidate the first iteration unless it blocks safe operation.

- When MCP GitHub tool names differ from expected names
- Then skill instructions should describe using available MCP GitHub/plugin capability rather than hard-code an unavailable tool.

## Error Cases

- If any required skill file is missing
- Then verification fails.

- If any GitHub skill lacks the shared handoff contract
- Then verification fails.

- If implementation drifts into GitHub Actions, templates, Projects sync, or core skill rewrites
- Then verification fails against scope.

## Expected Outcomes

- OpenSpec validation passes.
- Skill and docs coverage matches the approved brief and spec.
- Scope remains focused on orchestration skills and workflow docs.

## Notes for Implementation

- Use this scenario as final review checklist before `/review`.
- Do not require live GitHub mutation during scenario verification.
