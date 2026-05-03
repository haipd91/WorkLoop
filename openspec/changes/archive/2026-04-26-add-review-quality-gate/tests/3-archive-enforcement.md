# Test Scenario: 3 archive enforcement

- Source tasks:
  - 3.1 Block archive when `review.md` is missing
  - 3.2 Block archive when metadata is malformed
  - 3.3 Block archive when verdict is not `pass`
  - 3.4 Block archive when `blocking_findings: present`
  - 3.5 Block archive when review is stale
  - 3.6 Present review verdict and residual risk
  - 3.7 Ensure exact failure messages
- Related requirements:
  - Require post-apply review artifact
  - Define parseable review artifact contract
  - Block archive on failing review
  - Block archive on stale review
  - Preserve human archive acceptance
- Assumptions:
  - `/archive` performs review gate checks before moving the change directory.
  - Existing artifact/task warnings remain separate from review hard blockers.

## Happy Path

- Khi `/archive <name>` runs after `/review` passes
- And `review.md` has `verdict: pass`
- And `review.md` has `blocking_findings: none`
- And review state matches current implementation state
- Thì `/archive` may continue to remaining archive checks.

- Khi review gate passes
- Thì `/archive` presents review verdict and residual risk before final archive action.

## Edge Cases

- Khi artifact/task completion warnings exist but review gate passes
- Thì `/archive` may continue existing warning/confirmation behavior for those warnings.

- Khi multiple review gate failures exist
- Thì `/archive` reports exact failing conditions, not a generic archive failure.

- Khi legacy bypass is documented with passing metadata
- Thì `/archive` may continue to remaining checks.

## Error Cases

- Khi `review.md` is missing
- Thì `/archive` blocks and instructs user to run `/review <name>`.

- Khi `review.md` is malformed or unparseable
- Thì `/archive` blocks and reports malformed review metadata.

- Khi `verdict: fail`
- Thì `/archive` blocks and reports non-pass verdict.

- Khi `blocking_findings: present`
- Thì `/archive` blocks and reports unresolved blocking findings.

- Khi current implementation state differs from `reviewed_ref` or `reviewed_diff`
- Thì `/archive` blocks and instructs user to rerun `/review`.

- Khi pre-gate legacy change has no valid review and no documented bypass
- Thì `/archive` blocks.

## Expected Outcomes

- Review gate failures are hard blockers, not warnings.
- Archive does not move the change directory when review evidence fails.
- Failure messages identify the exact recovery path.

## Notes for Implementation

- Review gate should run before the destructive archive move.
- Keep review blockers separate from existing archive warnings.
