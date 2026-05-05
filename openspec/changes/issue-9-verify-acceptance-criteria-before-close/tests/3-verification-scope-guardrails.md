# Test Scenario: 3. Verification And Scope Guardrails

- Source tasks:
  - 3.1 Run OpenSpec validation for `issue-9-verify-acceptance-criteria-before-close`.
  - 3.2 Review generated tasks/tests coverage against the brief acceptance criteria.
  - 3.3 Confirm no new dependency, GitHub Action, or issue template migration was introduced.
- Related acceptance criteria:
  - `/github:merge` checks linked issue Acceptance Criteria before closing the issue.
  - `/github:merge` maps each criterion to durable evidence instead of blindly ticking boxes.
  - Criteria with sufficient evidence are updated to checked state in the issue body before close.
  - Criteria without sufficient evidence block merge/close or require explicit documented human bypass.
  - Ambiguous or unparseable criteria are reported clearly in the handoff.
  - The workflow distinguishes final AC closure verification from full implementation review.
  - Specs and skill instructions document the expected evidence sources.
- Assumptions:
  - Scenario tests are used because no clear test framework was detected in this repository.

## Happy Path

- When implementation finishes
- Then `openspec validate issue-9-verify-acceptance-criteria-before-close --strict` passes.
- And `tasks.md` checkboxes are complete.
- And tests cover workflow contract, merge skill AC flow, and scope guardrails.

- When reviewing changed files
- Then changes are limited to spec deltas, `.codex/skills/github-merge/SKILL.md`, and necessary OpenSpec artifacts.
- And no new dependency, GitHub Action, data model, custom GitHub client, or issue template migration appears.

## Edge Cases

- Implementation adds cross-references to `/github:create-issue`, `/github:post-review`, or `/github:sync-review`.
- Then those changes are accepted only if they are narrow evidence/context references and do not move closure mutation away from `/github:merge`.

- Implementation documents parseable checklist forms.
- Then it keeps the rule instruction-level and fails closed on unsupported formats.

## Error Cases

- OpenSpec validation fails.
- The tests do not cover checked-but-unsupported criteria, auto-close ordering, uneditable issue body, ambiguous criteria, and durable bypass.
- Implementation introduces a GitHub Action, parser dependency, issue template migration, or new evidence database.
- Implementation makes `/review` responsible for editing GitHub issue checkboxes.
- Implementation allows chat-only bypass.

## Expected Outcomes

- Verification proves the planning artifacts and skill instructions satisfy the issue #9 acceptance criteria.
- Scope stays small and instruction-driven.
- `/apply` has concrete scenario coverage before implementation starts.

## Notes for Implementation

- Do not run implementation directly from `/tdd`.
- Use these scenarios as review checklist input during `/apply` and `/review`.
