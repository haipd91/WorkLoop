# Brief: Verify Acceptance Criteria Before Close

## Problem

`/github:create-issue` creates GitHub issues with `Acceptance Criteria` checkboxes, but the later GitHub-backed workflow does not clearly show those criteria being checked before the issue is closed.

This weakens the delivery record: an issue can be closed while acceptance criteria still appear unchecked, or checkboxes can be ticked mechanically without evidence.

## Goal

Before `/github:merge` closes a linked issue, the workflow verifies each acceptance criterion against durable evidence and updates the issue checkbox state only for criteria that are actually covered.

## Scope

### In scope

- Define `/github:merge` behavior for reading linked issue `## Acceptance Criteria` checkboxes before closing the issue.
- Require mapping each criterion to evidence from `review.md`, `tasks.md`, tests, PR verification, or explicit human bypass.
- Require updating issue body checkboxes from `[ ]` to `[x]` only after evidence review.
- Block merge or close when parseable criteria remain unchecked without evidence or explicit accepted bypass.
- Document handoff and blocker output for uncovered or ambiguous criteria.

### Out of scope

- Do not perform a full second implementation review during merge.
- Do not auto-check all criteria merely because PR checks or review pass.
- Do not add a new GitHub Action or external automation unless a later design explicitly requires it.
- Do not change GitHub issue template structure beyond the existing `## Acceptance Criteria` section unless needed by the proposal.

## Constraints

- `/review` remains the deep implementation review gate.
- `/github:merge` performs final acceptance-criteria closure verification, not a full review rerun.
- Checkbox updates must be evidence-backed.
- Missing evidence must block close or require explicit documented human bypass.
- GitHub issue #9 is the durable intake record.

## Acceptance Criteria

- [ ] `/github:merge` checks linked issue Acceptance Criteria before closing the issue.
- [ ] `/github:merge` maps each criterion to durable evidence instead of blindly ticking boxes.
- [ ] Criteria with sufficient evidence are updated to checked state in the issue body before close.
- [ ] Criteria without sufficient evidence block merge/close or require explicit documented human bypass.
- [ ] Ambiguous or unparseable criteria are reported clearly in the handoff.
- [ ] The workflow distinguishes final AC closure verification from full implementation review.
- [ ] Specs and skill instructions document the expected evidence sources.

## Links / Context

- GitHub issue: https://github.com/haipd91/WorkLoop/issues/9
- Discussion decision: merge-time checking has value only if it verifies acceptance criteria coverage before ticking checkboxes and closing the issue.
