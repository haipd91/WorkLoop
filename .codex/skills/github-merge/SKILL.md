---
name: github:merge
description: Check GitHub and OpenSpec release gates, merge a PR through GitHub MCP/plugin, close the issue if needed, and hand off to archive.
license: MIT
compatibility: Requires GitHub MCP/plugin access and OpenSpec review artifact.
---

# GitHub Merge

Use this skill when PR work is complete and the user wants to merge the GitHub PR, close the linked issue if needed, and continue to OpenSpec archive.

## Shared Contract

- Use the GitHub MCP/plugin for GitHub PR, issue, review, check, merge, and close operations.
- Do not ask the user to paste tokens, personal access tokens, or secrets into chat.
- If GitHub MCP/plugin is unavailable or lacks permission, stop and produce a blocked handoff.
- Do not create a custom GitHub HTTP client.
- This is a mutating GitHub skill. State the PR and issue target before merge/close.

## Input

Accept any of:

- PR number or URL
- OpenSpec change name
- linked issue number or URL
- merge strategy if the human specifies one

If merge strategy is not specified, use the repository default exposed by GitHub MCP/plugin or block if the default cannot be determined safely.

## Gates

Before merge, verify:

- PR target is clear.
- Linked issue is known or missing linkage is explicitly accepted by the human.
- PR has no unresolved blocking comments or requested changes.
- Required checks/CI are passing, skipped, or explicitly bypassed by the human.
- `openspec/changes/<change-name>/review.md` exists.
- `review.md` has:
  - `verdict: pass`
  - `blocking_findings: none`
- OpenSpec change path is known for archive handoff.

## Workflow

1. Resolve PR, repository, linked issue, and OpenSpec change.
2. Read PR review/comment/check state through GitHub MCP/plugin.
3. Read local `review.md` metadata.
4. If any gate fails, block and return handoff.
5. Merge the PR through GitHub MCP/plugin.
6. Close the linked issue if GitHub did not auto-close it and closing is appropriate.
7. Return the handoff to `/archive <change-name>`.

## Handoff

Every result, including blocked states, must end with:

```md
## Handoff

- Current state:
- Done:
- Needs human:
- Next command:
- Blockers:
- Links:
```

Successful next command:

```text
/archive <change-name>
```

## Blocked States

Block instead of merging when:

- PR target is unclear
- GitHub MCP/plugin is unavailable
- required checks are failing or unreadable without accepted bypass
- unresolved blocking comments remain
- requested changes remain
- local `review.md` is missing, malformed, stale, or failing
- issue close behavior is unclear and cannot be safely inferred
