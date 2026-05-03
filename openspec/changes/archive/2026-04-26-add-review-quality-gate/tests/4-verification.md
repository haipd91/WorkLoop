# Test Scenario: 4 verification

- Source tasks:
  - 4.1 Verify documented workflow
  - 4.2 Verify missing review blocks archive
  - 4.3 Verify malformed review blocks archive
  - 4.4 Verify non-pass verdict blocks archive
  - 4.5 Verify blocking findings block archive
  - 4.6 Verify stale review blocks archive
  - 4.7 Verify passing current review proceeds
- Related requirements:
  - All review-quality-gate requirements
  - Hand off to /review after implementation
- Assumptions:
  - Verification may be manual or command-based because this repo has no detected runnable test framework.

## Happy Path

- Khi implementation updates workflow docs and skills
- Thì search results for active docs no longer show `/apply -> /archive` as direct next-step guidance.

- Khi a sample `review.md` has valid metadata, pass verdict, no blocking findings, and current review state
- Thì archive gate allows remaining archive checks.

## Edge Cases

- Khi active docs mention `/archive` in general
- Thì mention is acceptable only if `/review` remains the required prior step after `/apply`.

- Khi archived historical changes mention old flows
- Thì they do not count as active workflow regressions unless surfaced to users.

- Khi `review.md` records residual risk
- Thì `/archive` still presents that risk before final action.

## Error Cases

- Khi active `/apply` completion text still says ready to archive
- Thì verification fails.

- Khi `.codex/skills/review/SKILL.md` is absent
- Thì verification fails.

- Khi `/archive` can proceed with missing, malformed, failing, blocking, or stale `review.md`
- Thì verification fails.

- Khi review gate failures can be bypassed by generic warning confirmation
- Thì verification fails.

## Expected Outcomes

- Active workflow reads `/apply -> /review -> /archive`.
- `/review` creates durable review evidence.
- `/archive` blocks every review gate failure case.
- Passing current review can proceed to remaining archive checks.

## Notes for Implementation

- Use repository search to distinguish active workflow docs from archived historical artifacts.
- Verification should explicitly cover each blocker separately.
