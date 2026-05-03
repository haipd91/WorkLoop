# Test Scenario: Verification And Followups

- Source tasks:
  - 3.1 Run OpenSpec validation for the change and fix any spec or artifact issues.
  - 3.2 Review generated docs/specs against the brief acceptance criteria.
  - 3.3 Identify follow-up GitHub issues or OpenSpec changes for automation, skill behavior, templates, or GitHub Actions.
- Related requirements:
  - GitHub source of truth
  - Quality gates
  - Future automation boundary
- Assumptions:
  - Scenario tests are the correct mode because no project test framework is detected.
  - Follow-up work can be identified in docs or review notes without being implemented here.

## Happy Path

- When implementation is complete
- Then OpenSpec validation passes for `github-source-of-truth-workflow`.
- And the implemented docs/specs can be mapped back to every brief acceptance criterion.
- And follow-up items for automation, skill behavior, GitHub Actions, templates, or sync are identified without being added to current scope.

## Edge Cases

- When open questions remain about labels, templates, or whether every OpenSpec change requires an issue
- Then they are documented as follow-up decisions and do not block this docs/spec-only contract unless they undermine the core lifecycle.

- When validation passes but acceptance criteria are only partially covered
- Then the missing coverage is fixed before review readiness.

## Error Cases

- If OpenSpec validation fails
- Then the change is not ready for apply completion or review.

- If implementation satisfies docs but omits future-scope boundaries
- Then the change fails the acceptance criteria because later automation scope is not constrained.

- If follow-up automation or skill changes are implemented inside this change
- Then the change violates its own scope and must be split or reverted before review.

## Expected Outcomes

- Verification is based on OpenSpec validation and acceptance-criteria coverage.
- Follow-up work is visible but not mixed into current scope.
- The final PR can show evidence that this contract is docs/spec-only.

## Notes for Implementation

- Do not create runnable test code unless a framework is added later.
- Use these scenarios as review checklist input during `/apply` and `/review`.
