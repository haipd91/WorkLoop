# Test Scenario: Workflow Documentation

- Source tasks:
  - 5.1 Add or update docs showing the end-to-end GitHub orchestration flow around OpenSpec.
  - 5.2 Document the distinction between core OpenSpec skills and GitHub orchestration skills.
  - 5.3 Document read-only versus mutating GitHub skills.
  - 5.4 Document handoff contract and example handoffs for successful and blocked states.
  - 5.5 Link existing GitHub source-of-truth workflow docs to the orchestration workflow.
- Related requirements:
  - Workflow documentation
  - GitHub orchestration skills
  - Handoff contract
- Assumptions:
  - Existing workflow docs should remain the durable human guide.
  - The GitHub source-of-truth workflow doc exists or is part of nearby work in the same repo.

## Happy Path

- When a human reads the workflow documentation
- Then the docs show create issue -> OpenSpec workflow -> create PR -> post/sync review -> address comments -> merge -> archive.
- And the docs distinguish core OpenSpec skills from GitHub orchestration skills.
- And the docs distinguish read-only skills from mutating skills.
- And the docs include handoff contract fields and examples for successful and blocked states.
- And existing GitHub source-of-truth docs link to or consistently reference the orchestration workflow.

## Edge Cases

- When a change is trivial and does not need GitHub orchestration
- Then the docs should not imply every local change must use every GitHub skill.

- When GitHub orchestration cannot continue because of missing MCP GitHub access
- Then docs should point to handoff blockers rather than suggest manual token paste in chat.

## Error Cases

- If docs imply core OpenSpec skills are rewritten to own GitHub state
- Then the documentation violates the design.

- If docs omit handoff examples
- Then the documentation fails the human handoff requirement.

## Expected Outcomes

- Human users can see where each GitHub skill fits.
- Docs make next steps clear after each skill.
- Docs remain consistent with GitHub source-of-truth policy.

## Notes for Implementation

- Link docs instead of duplicating long policy sections.
- Keep the flow operational, not abstract.
