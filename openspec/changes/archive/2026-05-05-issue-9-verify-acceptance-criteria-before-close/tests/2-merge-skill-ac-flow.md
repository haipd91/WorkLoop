# Test Scenario: 2. Merge Skill AC Flow

- Source tasks:
  - 2.1 Update `.codex/skills/github-merge/SKILL.md` gates to include acceptance-criteria coverage before linked issue close.
  - 2.2 Update `/github:merge` workflow steps to read issue body, map criteria to evidence, update checked criteria, and block uncovered criteria.
  - 2.3 Update `/github:merge` blocked states and handoff expectations for uncovered, ambiguous, unparseable, or uneditable acceptance criteria.
- Related requirements:
  - Merge reads linked issue criteria
  - Merge maps criteria to evidence
  - Merge updates verified criteria
  - Merge blocks uncovered criteria
  - Merge reports ambiguous criteria
  - Merge avoids blind checkbox updates
- Assumptions:
  - GitHub MCP/plugin can read and update issue bodies when permissions allow.
  - Acceptance Criteria are markdown task-list items under `## Acceptance Criteria`.

## Happy Path

- Given a PR, linked issue, OpenSpec change, passing review metadata, clean review feedback, and passing or N/A checks
- And the linked issue has parseable `## Acceptance Criteria` checkboxes
- And each criterion maps to durable evidence from `review.md`, `tasks.md`, tests, verification output, PR notes, or durable bypass
- When `/github:merge` runs
- Then it reads the issue body before merge.
- And it builds a criterion-level coverage map with criterion text, checkbox state, coverage status, evidence source, and action taken.
- And it updates only verified unchecked criteria from `[ ]` to `[x]`.
- And it re-reads the issue body after update to verify expected state.
- And it proceeds to release approval, merge, issue close, and archive handoff only after AC closure verification passes.

## Edge Cases

- A criterion is already `[x]`.
- Then `/github:merge` still maps it to evidence and reports it as `left checked` only when evidence exists.

- A PR can auto-close the linked issue on merge.
- Then AC verification and issue body update happen before merge, preventing stale checkbox close.

- The issue body changes between read and update.
- Then `/github:merge` preserves non-AC text, changes only expected checkbox markers, re-reads, and blocks on drift or edit conflict.

- Human bypass is used.
- Then bypass is accepted only when documented in a durable PR/issue/OpenSpec artifact with criterion text and reason.

## Error Cases

- Linked issue is missing, multiple, or conflicts with change/PR references.
- Then `/github:merge` blocks and asks for durable target selection.

- The `## Acceptance Criteria` section is missing, duplicated, inside code/quote content, or unsafe to parse.
- Then `/github:merge` blocks with exact ambiguity in handoff.

- A criterion has no sufficient evidence.
- Then `/github:merge` blocks before human release approval and before merge.

- GitHub issue body cannot be updated or verified after update.
- Then `/github:merge` blocks rather than merging or closing with stale criteria.

## Expected Outcomes

- `/github:merge` has a pre-merge AC closure gate.
- The issue body only changes checkbox markers for verified criteria.
- Handoff lists covered, uncovered, ambiguous, unparseable, uneditable, and bypassed criteria.
- Blind checkbox updates from review pass, CI pass, or GitHub review approval are rejected.

## Notes for Implementation

- The skill instructions should be explicit enough for instruction-driven execution; no parser dependency is required.
- Handoff should include links or paths to evidence where possible.
