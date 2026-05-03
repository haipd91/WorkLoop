# Test Scenario: Review Feedback Skills

- Source tasks:
  - 3.1 Add `/github:sync-review` skill instructions as a read-only PR review/comment/check sync and action-plan generator.
  - 3.2 Add `/github:address-comments` skill instructions for handling inline file comments and general PR/review comments.
  - 3.3 Define ambiguity and scope-conflict stop conditions for `/github:address-comments`.
- Related requirements:
  - Sync review workflow
  - Address comments workflow
  - Handoff contract
  - MCP GitHub usage
- Assumptions:
  - Review comments may be inline file comments or general PR/review comments.
  - MCP GitHub/plugin may not support replying to every comment type equally.

## Happy Path

- When `/github:sync-review` reads a PR with unresolved feedback
- Then it summarizes inline comments, general comments, requested changes, and CI/check failures by type.
- And it includes blockers, links, and a recommended next command.
- And it does not mutate local files, GitHub state, comments, or checks.

- When `/github:address-comments` receives clear actionable comments
- Then it maps inline comments to files/lines when possible.
- And it maps general comments to tasks or files when possible.
- And it applies focused fixes, runs relevant verification, pushes the branch, and replies to comments when MCP GitHub/plugin supports it.

## Edge Cases

- When a general comment has no file location
- Then `/github:address-comments` treats it as a general task or asks the human if no scoped fix is clear.

- When an inline comment points to stale code
- Then `/github:address-comments` identifies the stale mapping and asks the human or records a blocker instead of guessing.

- When MCP GitHub/plugin does not support replying to a specific comment type
- Then `/github:address-comments` records the unsupported reply action in the handoff.

## Error Cases

- If `/github:sync-review` edits files, replies to comments, pushes commits, or changes GitHub state
- Then the read-only sync contract fails.

- If `/github:address-comments` guesses on ambiguous or out-of-scope feedback
- Then the address-comments workflow fails.

## Expected Outcomes

- Review sync produces a reliable action plan without side effects.
- Address-comments only mutates when feedback is clear and scoped.
- Both inline and general comments are represented in the workflow.

## Notes for Implementation

- Separate reading review state from fixing review state.
- Verification should be relevant to the files or behavior changed.
