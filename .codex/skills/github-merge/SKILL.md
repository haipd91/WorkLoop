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

## Acceptance Criteria Closure

For GitHub-backed work with a linked issue, `/github:merge` must verify the linked issue `## Acceptance Criteria` section before human release approval, PR merge, or issue close.

Parseable criteria are markdown task-list items under `## Acceptance Criteria`, such as:

```md
- [ ] Criterion text
- [x] Criterion text
```

Fail closed when the criteria section is missing, duplicated, nested ambiguously, inside quote/code content, unsafe to parse, or unsafe to edit.

For every criterion, including criteria already checked before merge time, build a coverage record:

- criterion text
- current checkbox state
- coverage status: `covered`, `uncovered`, `ambiguous`, or `bypassed`
- evidence source: `review.md`, `tasks.md`, tests, verification output, PR note, issue/PR comment, or explicit durable bypass
- action taken: `checked`, `left checked`, `blocked`, or `bypassed`

Do not treat `review.md: pass`, CI pass, or GitHub review approval as blanket evidence for every criterion. These can support evidence, but each criterion still needs its own mapping.

When updating issue checkboxes:

- Re-read the latest issue body immediately before editing.
- Preserve all non-AC issue body text.
- Change only checkbox markers for criteria with sufficient evidence.
- Re-read the issue body after editing and verify expected checkbox state.
- Block if the issue body changed unexpectedly, cannot be edited, or cannot be verified after edit.

Human bypass counts only when documented in a durable GitHub or committed OpenSpec artifact, preferably a PR or issue comment that includes the criterion text and reason. Chat-only bypass is not sufficient.

## Gates

Before merge, verify:

- PR target is clear.
- Linked issue is known or missing linkage is explicitly accepted and durably documented by the human.
- Linked issue acceptance criteria have been read, mapped to criterion-level durable evidence, updated in the issue body when needed, and verified before merge.
- Already checked acceptance criteria are also mapped to durable evidence or documented bypass.
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
3. Read local `review.md` metadata and available evidence sources, including `tasks.md`, generated tests, verification output, PR verification notes, and durable bypass records.
4. Classify checks as passing, blocked, bypassed, or N/A.
5. Read the linked issue body and locate exactly one parseable `## Acceptance Criteria` section.
6. Identify checked and unchecked criteria under that section.
7. Map every criterion to criterion-level durable evidence or a durable bypass record.
8. Re-read the issue body immediately before any checkbox update.
9. Update only checkbox markers for verified unchecked criteria.
10. Re-read the issue body after update and verify the expected checkbox state.
11. If acceptance criteria are uncovered, ambiguous, unparseable, uneditable, stale after update, or checked without evidence, block and return a handoff listing the affected criteria.
12. If GitHub reviewer approval is required by branch protection or repository policy and missing, block and return handoff.
13. If any other gate fails, block and return handoff.
14. If the PR is draft, mark it ready for review through GitHub MCP/plugin.
15. Confirm explicit human release approval unless already provided in the merge request.
16. Merge the PR through GitHub MCP/plugin.
17. Close the linked issue if GitHub did not auto-close it and closing is appropriate.
18. Return the handoff to `/archive <change-name>`.

## Handoff

Every result, including blocked states, must end with:

```md
## Handoff

- Current state:
- Done:
- Needs human:
- Next command:
- Blockers:
- Acceptance criteria:
  - Covered:
  - Already checked with evidence:
  - Uncovered:
  - Ambiguous/unparseable:
  - Uneditable:
  - Bypassed:
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
- linked issue target is missing, multiple, conflicting, or not durably accepted by the human
- linked issue `## Acceptance Criteria` section is missing, duplicated, ambiguous, unparseable, or unsafe to edit
- any acceptance criterion, checked or unchecked, lacks criterion-level durable evidence or durable bypass
- issue checkbox updates cannot be applied or verified after re-reading the issue body
- issue close behavior is unclear and cannot be safely inferred

Do not block solely because:

- the PR is draft after all gates pass; mark it ready instead
- GitHub exposes no required checks or statuses; record checks as N/A
- no GitHub approval review exists when branch protection and repo policy do not require one
