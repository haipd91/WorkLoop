# Test Scenario: 1. Spec Workflow Contract

- Source tasks:
  - 1.1 Verify `github-workflow` delta requires linked issue acceptance criteria verification before issue close.
  - 1.2 Verify `github-orchestration-skills` delta requires `/github:merge` to read, parse, and evaluate linked issue criteria before merge or close.
  - 1.3 Confirm specs distinguish acceptance-criteria closure verification from full `/review` implementation review.
- Related requirements:
  - `github-workflow`: Issue acceptance criteria closure
  - `github-orchestration-skills`: Merge verifies acceptance criteria closure
- Assumptions:
  - This change modifies workflow/spec/skill instructions only.
  - `/review` remains the deep implementation review gate.

## Happy Path

- When reading the `github-workflow` spec delta
- Then it requires linked GitHub issue acceptance criteria to be verified against durable evidence before issue close.
- And it requires verified criteria to be reflected in issue checkbox state.

- When reading the `github-orchestration-skills` spec delta
- Then it requires `/github:merge` to read linked issue Acceptance Criteria before merge or close.
- And it requires `/github:merge` to map each criterion to durable evidence.
- And it rejects blind checkbox updates from broad pass signals.

## Edge Cases

- A criterion is already checked before merge time.
- The specs still require evidence mapping for that criterion, because pre-checked boxes can be mechanical or stale.

- Acceptance criteria are present but ambiguous or unsafe to parse.
- The specs require handoff reporting and fail-closed behavior rather than silent close.

## Error Cases

- The workflow spec only blocks issue close after merge, allowing PR merge before AC verification.
- The orchestration spec treats `review.md: pass` as enough to check every criterion.
- The specs imply `/review` should update GitHub issue checkboxes.

## Expected Outcomes

- Specs make AC closure a final merge/issue-close gate.
- Specs keep `/review` as evidence source, not closure-state mutator.
- Specs support blocking uncovered, ambiguous, or unsupported criteria.

## Notes for Implementation

- If spec wording conflicts between "block close" and "block merge or close", prefer the stricter design decision: block before merge when AC closure cannot be verified.
- Keep delta scope to `github-workflow` and `github-orchestration-skills`.
