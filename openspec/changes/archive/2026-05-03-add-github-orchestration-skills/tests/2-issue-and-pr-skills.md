# Test Scenario: Issue And PR Skills

- Source tasks:
  - 2.1 Add `/github:create-issue` skill instructions for creating issues and handing off issue URL/number plus suggested OpenSpec change name.
  - 2.2 Add `/github:create-pr` skill instructions for creating or updating draft PRs with issue link, OpenSpec path, summary, verification, and residual risk.
  - 2.3 Add `/github:post-review` skill instructions for posting `review.md` verdict, findings, tests, and residual risk to a PR.
- Related requirements:
  - Create issue workflow
  - Create PR workflow
  - Post review workflow
  - Handoff contract
  - MCP GitHub usage
- Assumptions:
  - Issue and PR operations are mutating GitHub operations.
  - OpenSpec change names should be suggested but not require a separate `/github:link-work` skill.

## Happy Path

- When `/github:create-issue` receives enough work information
- Then it creates a GitHub issue through MCP GitHub/plugin.
- And it hands off issue number, issue URL, suggested OpenSpec change name, next command, blockers, and links.

- When `/github:create-pr` runs from a work branch with enough context
- Then it creates or updates a draft PR.
- And the PR body includes issue link, OpenSpec path, problem, approach, verification, and residual risk.

- When `/github:post-review` finds a valid `review.md`
- Then it posts verdict, blocking findings state, findings summary, tests run, residual risk, and review artifact path/link to the PR.

## Edge Cases

- When issue input lacks acceptance criteria
- Then `/github:create-issue` asks for clarification or creates a clearly marked incomplete/draft issue only after explicit human acceptance.

- When `/github:create-pr` cannot identify an issue link or OpenSpec change path
- Then it asks the human or blocks with a handoff instead of creating a misleading PR.

- When `review.md` is missing or malformed
- Then `/github:post-review` blocks and hands off the command needed to produce review evidence.

## Error Cases

- If `/github:create-pr` creates a PR without issue linkage and OpenSpec path while pretending the PR is ready
- Then the create-PR workflow fails.

- If `/github:post-review` posts a review summary without verdict or blocking findings state
- Then the post-review workflow fails.

## Expected Outcomes

- Issue creation, PR creation, and review posting are independently usable and produce durable GitHub links.
- Each skill hands the human to the next OpenSpec or GitHub step.

## Notes for Implementation

- Draft PR is the default while verification or review is incomplete.
- Missing linkage should be explicit, not guessed.
