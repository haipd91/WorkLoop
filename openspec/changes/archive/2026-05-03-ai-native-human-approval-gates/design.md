## Context

The current workflow caught an important failure mode on PR #3: after `/review` created `review.md`, committing that artifact changed HEAD and made `reviewed_ref` stale. This is not an implementation change, but the current gate treats it like one.

The larger problem is workflow ownership. Codex should operate mechanical steps end-to-end. The human should approve material release risk, not manually interpret draft state, approval state, check absence, or artifact commit semantics.

Issue: https://github.com/haipd91/WorkLoop/issues/4

## Goals / Non-Goals

**Goals:**

- Make review metadata describe reviewed implementation state, not merely current HEAD.
- Allow review-artifact-only commits after review without stale gate failure.
- Let `/github:merge` mark PRs ready when gates pass.
- Separate GitHub reviewer approval from human release approval.
- Treat absent checks as N/A when no required checks are configured or exposed.
- Require human release approval for merge, then let Codex perform merge/archive steps.

**Non-Goals:**

- Bypass required branch protection.
- Hide failing CI.
- Remove review artifact metadata validation.
- Replace GitHub PR review with chat approval when branch protection requires GitHub reviewer approval.

## Decisions

### Decision: Review tracks implementation state

`review.md` should track implementation state through metadata that excludes the review artifact itself. Existing `reviewed_ref` can remain for traceability, but stale checks must compare current implementation state against the reviewed implementation state.

Alternative considered: never commit `review.md` before merge/archive. Rejected because the GitHub source-of-truth workflow expects committed OpenSpec artifacts.

### Decision: Review artifact commits are allowed

If the only post-review change is `openspec/changes/<name>/review.md`, merge/archive should not fail stale review. Any non-review file change still requires rerunning `/review`.

Alternative considered: automatically rerun review after committing `review.md`. Rejected because it creates a loop unless metadata semantics change anyway.

### Decision: AI owns mechanical PR readiness

If PR gates pass and the PR is draft, `/github:merge` should mark it ready before merging. The human should not need to click “Ready for review.”

Alternative considered: block on draft PR. Rejected because draft state is mechanical once gates pass.

### Decision: Human release approval is separate from GitHub approval

GitHub approval is required only when branch protection or explicit repo policy requires it. Otherwise, human approval in the merge request can satisfy release/risk approval.

Alternative considered: always require GitHub approval. Rejected because single-owner repos may not have reviewer policy and self-approval is weak.

## Risks / Trade-offs

- [Risk] A meaningful implementation change is misclassified as review-only -> Mitigation: compare pathsets and fingerprints excluding only `openspec/changes/<name>/review.md`.
- [Risk] Missing checks hide CI failures -> Mitigation: distinguish no checks exposed from failing/pending required checks.
- [Risk] Human approval in chat is not durable -> Mitigation: merge/PR comments should record approval when feasible.
- [Risk] Branch protection rules are not fully exposed -> Mitigation: block if GitHub rejects merge or required protection cannot be determined safely.

## Migration Plan

1. Update review artifact metadata contract.
2. Update `/review`, `/archive`, and `/github:merge` guidance.
3. Update `/github:sync-review` wording for no-checks/N/A state if needed.
4. Add tests covering review-artifact-only commits, draft readiness, approvals, and check states.

Rollback: revert guidance/spec updates. Existing fail-closed review gate remains available.

## Resolved Questions

- Metadata names: keep `reviewed_ref` and `reviewed_diff` for traceability, add `reviewed_implementation_diff` and `reviewed_paths_excluded`.
- Human release approval durability: prefer a PR comment when the GitHub connector supports it; otherwise record the approval in the merge handoff with residual risk.
