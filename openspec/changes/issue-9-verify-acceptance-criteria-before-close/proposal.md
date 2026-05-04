## Why

GitHub issues currently carry `Acceptance Criteria` checkboxes, but the delivery workflow does not require those criteria to be verified and checked before the linked issue is closed. This leaves issue closure evidence ambiguous and can make unchecked or mechanically checked criteria look equivalent to verified completion.

## What Changes

- Add a final acceptance-criteria closure gate to `/github:merge` before linked issue close.
- Require `/github:merge` to read linked issue `## Acceptance Criteria` checkboxes and map each criterion to durable evidence.
- Require evidence-backed criteria to be checked in the issue body before close.
- Block merge or issue close when criteria remain uncovered, ambiguous, or unparseable unless the human explicitly accepts and documents a bypass.
- Clarify that this gate is not a full second implementation review; `/review` remains the deep implementation review gate.

## Existing Context Reviewed

- `openspec/specs/github-workflow/spec.md`: Defines GitHub issues as durable intake records and merge/archive quality gates; impact is a modified quality-gate requirement for issue closure.
- `openspec/specs/github-orchestration-skills/spec.md`: Defines `/github:merge` gates, handoff order, and GitHub operation behavior; impact is a modified merge-gate requirement and handoff behavior.
- `openspec/specs/review-quality-gate/spec.md`: Defines `review.md` metadata and archive enforcement; impact is evidence source alignment without moving deep review responsibility into merge.
- `openspec/changes/issue-9-verify-acceptance-criteria-before-close/brief.md`: Current issue-backed brief; this is the active change being proposed, not an overlap conflict.

## Capabilities

### New Capabilities

- None.

### Modified Capabilities

- `github-workflow`: Add requirement that issue acceptance criteria must be verified and reflected before GitHub-backed issue closure.
- `github-orchestration-skills`: Add `/github:merge` behavior for acceptance-criteria evidence mapping, checkbox update, blocking, and handoff reporting.

## Impact

- Affected specs: `github-workflow`, `github-orchestration-skills`.
- Affected skill instructions: `.codex/skills/github-merge/SKILL.md`.
- No new runtime dependency, GitHub Action, data model, or external automation is required.
- GitHub issue #9 remains the durable intake record for this change.
