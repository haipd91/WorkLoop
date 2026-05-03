## Why

WorkLoop now has a GitHub source-of-truth contract, but it still lacks practical GitHub orchestration skills for issue creation, PR creation, PR review sync, comment handling, merge, and human handoff. Without these skills, the workflow can remain local/OpenSpec-driven while GitHub state is handled manually and inconsistently.

This change adds a dedicated GitHub orchestration layer around the existing OpenSpec workflow instead of rewriting core OpenSpec skills.

## What Changes

- Add new GitHub orchestration skills:
  - `/github:create-issue`
  - `/github:create-pr`
  - `/github:post-review`
  - `/github:sync-review`
  - `/github:address-comments`
  - `/github:merge`
- Require GitHub operations to use the MCP GitHub/plugin path, not custom HTTP clients or chat-pasted tokens.
- Define read-only versus mutating skill boundaries.
- Define a required handoff output contract for every GitHub orchestration skill.
- Support inline file comments and general PR/review comments in the review sync and address-comments flow.
- Document the end-to-end GitHub orchestration workflow around the existing OpenSpec skills.
- Link or update existing workflow docs so the GitHub source-of-truth contract and orchestration skills remain consistent.

## Capabilities

### New Capabilities

- `github-orchestration-skills`: Defines GitHub orchestration skill requirements, MCP usage, safety boundaries, handoff contract, PR review/comment handling, merge gates, and workflow documentation.

### Modified Capabilities

- None.

## Impact

- Adds new local skill instruction files for GitHub orchestration.
- Updates workflow documentation to show where GitHub orchestration skills fit around `/brief`, `/propose`, `/tdd`, `/apply`, `/review`, and `/archive`.
- Does not rewrite the core OpenSpec skills.
- Does not add GitHub Actions, issue/PR templates, GitHub Projects sync, or custom GitHub API clients.
