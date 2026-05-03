## Why

After `/apply`, the workflow has no mandatory review gate before archive. That allows a change to be archived without an explicit check against scope, correctness, verification, and remaining risk.

## What Changes

- Add a required `/review` quality gate between `/apply` and `/archive`.
- Require a review record for applied changes before they can be archived.
- Define review output expectations: findings, tests run, residual risk, and verdict.
- Block archive when the review record is missing, the verdict is not pass, or blocking findings remain.
- Update apply guidance so implementation handoff points to `/review` before archive.

## Capabilities

### New Capabilities

- `review-quality-gate`: Defines the post-apply `/review` gate, required review artifact, archive blocking behavior, and apply handoff guidance.

### Modified Capabilities

- `code-skill`: `/apply` handoff behavior changes so users are guided to run `/review` after implementation rather than proceeding toward archive.

## Impact

- Affected workflow skills/docs: `/apply`, `/review`, and `/archive`.
- Affected OpenSpec artifacts: change-local review artifact such as `review.md`.
- No new dependency.
- No application runtime behavior change.
