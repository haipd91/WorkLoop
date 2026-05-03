# Test Scenario: Merge Skill

- Source tasks:
  - 4.1 Add `/github:merge` skill instructions for checking review, CI, issue, and local `review.md` gates before merge.
  - 4.2 Define issue closing behavior and `/archive <change-name>` handoff after merge.
- Related requirements:
  - Merge workflow
  - Handoff contract
  - MCP GitHub usage
- Assumptions:
  - Merge is a mutating GitHub operation.
  - OpenSpec archive remains separate and is not performed by `/github:merge`.

## Happy Path

- When PR review state, CI/check state, issue linkage, local `review.md`, and unresolved comment state all satisfy gates
- Then `/github:merge` merges the PR through MCP GitHub/plugin.
- And it closes the linked issue if GitHub did not auto-close it.
- And it hands off `/archive <change-name>` with PR link, issue link, merge state, blockers, and next command.

## Edge Cases

- When the PR auto-closes the issue through GitHub close syntax
- Then `/github:merge` records that no manual close is needed.

- When the repository has a default merge strategy
- Then `/github:merge` uses or documents that default unless the human specifies another supported strategy.

- When checks are skipped but human accepts the risk
- Then `/github:merge` records the accepted bypass in handoff before merge.

## Error Cases

- If `/github:merge` merges while required CI/checks fail
- Then the merge gate fails.

- If `/github:merge` merges with unresolved blocking comments or requested changes
- Then the merge gate fails.

- If `/github:merge` archives the OpenSpec change itself
- Then it violates the workflow boundary; archive must remain a separate handoff.

## Expected Outcomes

- Merge happens only after gates pass or human-accepted bypass is explicit.
- Issue closure and archive handoff are durable and clear.

## Notes for Implementation

- Do not hide missing `review.md`; block and point to `/review <change-name>`.
- Merge should not proceed if GitHub state cannot be read.
