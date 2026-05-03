# Test Scenario: Workflow Contract

- Source tasks:
  - 1.1 Add or update documentation that defines GitHub Issues and Pull Requests as WorkLoop's durable source of truth.
  - 1.2 Document the issue -> OpenSpec change -> branch/PR -> review -> archive lifecycle.
  - 1.3 Document ownership boundaries for GitHub Issues, Pull Requests, branches, commits, and OpenSpec artifacts.
- Related requirements:
  - GitHub source of truth
  - Issue intake lifecycle
  - OpenSpec artifact ownership
  - Branch and pull request lifecycle
- Assumptions:
  - This is a docs/spec-only change.
  - GitHub is the repository-level source of truth, while OpenSpec remains the committed structured artifact system.

## Happy Path

- When a reader reviews the workflow documentation
- Then they can identify GitHub Issues and Pull Requests as the durable source of truth for delivery state.
- And they can trace the lifecycle from issue intake to OpenSpec change, branch/PR, review, merge, and archive.
- And they can identify which artifact owns demand, priority, scope, requirements, design, tasks, tests, review, and delivery evidence.

## Edge Cases

- When an issue does not contain clear problem context, expected outcome, or acceptance criteria
- Then the workflow states that clarification or added context is required before implementation tasks are created.

- When a material decision exists only in chat
- Then the workflow states that it must be captured in a GitHub issue, PR, or committed OpenSpec artifact before review readiness.

## Error Cases

- If documentation implies chat history can be the source of truth for material decisions
- Then the workflow contract fails the source-of-truth requirement.

- If documentation does not distinguish GitHub ownership from OpenSpec ownership
- Then the workflow contract fails the artifact ownership requirement.

## Expected Outcomes

- Documentation defines GitHub source-of-truth behavior without replacing OpenSpec artifacts.
- Lifecycle steps are concrete enough to guide future skill behavior.
- Ownership boundaries are explicit and not duplicated across artifacts.

## Notes for Implementation

- Do not implement automation in this change.
- Avoid copying full OpenSpec artifact content into GitHub examples; link ownership rules instead.
