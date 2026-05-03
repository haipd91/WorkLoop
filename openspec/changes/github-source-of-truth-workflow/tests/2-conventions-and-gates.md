# Test Scenario: Conventions And Gates

- Source tasks:
  - 2.1 Document naming conventions for issue references, OpenSpec changes, branches, commits, and PR titles/bodies.
  - 2.2 Document quality gates for PR readiness, merge readiness, and OpenSpec archive readiness.
  - 2.3 Document that GitHub automation, skill behavior changes, GitHub Actions, templates, and status sync are future scope.
- Related requirements:
  - Branch and pull request lifecycle
  - Naming conventions
  - Quality gates
  - Future automation boundary
- Assumptions:
  - Branches use the repository convention of the `codex/` prefix.
  - Quality gates are policy/docs in this change, not enforced by automation.

## Happy Path

- When a GitHub issue is converted into a WorkLoop change
- Then the documentation defines a kebab-case OpenSpec change name that preserves a stable connection to the issue or outcome.
- And the branch naming convention includes `codex/` plus a stable issue or change slug when practical.
- And the PR body convention includes problem, approach, verification, linked issue, and OpenSpec change path.

- When a PR is marked ready for review
- Then the documentation requires scope, requirements, implementation plan, and verification evidence to exist in GitHub or committed OpenSpec artifacts.

- When an OpenSpec change is archived
- Then the documentation requires review requirements and GitHub delivery evidence to be satisfied.

## Edge Cases

- When a change does not have a GitHub issue yet
- Then the documentation should still define how to preserve a stable link to the change or outcome, and should avoid pretending an issue number exists.

- When a PR references an issue but does not close it directly
- Then the PR body can still link the issue, but close/fix syntax should only be required when the PR actually closes or fixes the issue.

## Error Cases

- If the implementation adds GitHub automation, skill behavior changes, GitHub Actions, template migration, or automatic status sync
- Then the change violates the future automation boundary.

- If quality gates are described only as optional suggestions
- Then the workflow does not satisfy the PR readiness, merge readiness, or archive readiness requirements.

## Expected Outcomes

- Naming conventions are predictable and concrete.
- PR and archive gates are verifiable from repo and GitHub artifacts.
- Automation and enforcement remain explicitly out of scope.

## Notes for Implementation

- Keep examples practical but non-binding where issue numbers may not exist.
- Separate "must be documented" from "must be automated".
