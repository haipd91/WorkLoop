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
- If the PR is draft and every other gate passes, mark it ready for review through GitHub MCP/plugin before merging.
- PR has no unresolved blocking comments or requested changes.
- Required checks/CI are passing or skipped.
- If GitHub exposes no required checks or statuses for the PR head commit, treat checks as N/A and record that state instead of blocking.
- Failing or pending required checks block unless the human explicitly accepts a documented bypass.
- GitHub reviewer approval is required only when branch protection or explicit repository policy requires it.
- Human release approval is required before merge unless that approval was already provided in the merge request.
- `openspec/changes/<change-name>/review.md` exists.
- `review.md` has:
  - `verdict: pass`
  - `blocking_findings: none`
  - non-stale implementation-state metadata, allowing review-artifact-only commits after review
- OpenSpec change path is known for archive handoff.

## Workflow

1. Resolve PR, repository, linked issue, and OpenSpec change.
2. Read PR review/comment/check state through GitHub MCP/plugin.
3. Read local `review.md` metadata.
4. Classify checks as passing, blocked, bypassed, or N/A.
5. If GitHub reviewer approval is required by branch protection or repository policy and missing, block and return handoff.
6. If any other gate fails, block and return handoff.
7. If the PR is draft, mark it ready for review through GitHub MCP/plugin.
8. Confirm explicit human release approval unless already provided in the merge request.
9. Merge the PR through GitHub MCP/plugin.
10. Close the linked issue if GitHub did not auto-close it and closing is appropriate.
11. Return the handoff to `/archive <change-name>`.

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
- required checks are failing or pending without accepted documented bypass
- GitHub reviewer approval is required by branch protection or repository policy and missing
- human release approval is missing
- unresolved blocking comments remain
- requested changes remain
- local `review.md` is missing, malformed, stale, or failing
- issue close behavior is unclear and cannot be safely inferred

Do not block solely because:

- the PR is draft after all gates pass; mark it ready instead
- GitHub exposes no required checks or statuses; record checks as N/A
- no GitHub approval review exists when branch protection and repo policy do not require one
