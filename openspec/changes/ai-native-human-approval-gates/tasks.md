## 1. Review Metadata Semantics

- [x] 1.1 Update `review-quality-gate` spec to define implementation-state metadata that can exclude `review.md`.
- [x] 1.2 Update `/review` guidance to write implementation-state metadata and residual risk.
- [x] 1.3 Update `/archive` guidance to allow review-artifact-only commits without stale failure.

## 2. AI-Native Merge Gates

- [x] 2.1 Update `github-workflow` spec to define Codex-owned mechanical steps and human release approval.
- [x] 2.2 Update `github-orchestration-skills` spec for draft PR readiness, approval semantics, and N/A checks.
- [x] 2.3 Update `/github:merge` guidance to mark draft PRs ready when gates pass.
- [x] 2.4 Update `/github:merge` guidance to require GitHub reviewer approval only when branch protection or repo policy requires it.
- [x] 2.5 Update `/github:merge` guidance to treat missing checks as N/A when no required checks are configured or exposed.
- [x] 2.6 Update `/apply` handoff so GitHub-backed changes route to `/github:create-pr` before `/review`.

## 3. Verification

- [x] 3.1 Generate test scenarios with `/tdd ai-native-human-approval-gates`.
- [x] 3.2 Run OpenSpec validation for `ai-native-human-approval-gates`.
- [x] 3.3 Verify scenarios cover review-artifact-only commits, draft PR readiness, human approval, GitHub approval, and check states.
