# Test Scenario: 5 docs and regression

- Source tasks:
  - 5.1 Update workflow docs
  - 5.2 Update impacted specs
  - 5.3 Verify phase 1 behavior still passes
- Related requirements:
  - All review artifact and review quality gate requirements
- Assumptions:
  - Archived historical mentions are not active workflow regressions.

## Happy Path

- Khi docs describe the workflow
- Thì they mention graph-aware `review` artifact.
- And they clarify `review: done` means `review.md` exists, not verdict pass.
- And they keep `/apply -> /review -> /archive`.

## Edge Cases

- Khi phase 1 `/review` skill writes `review.md`
- Thì behavior remains compatible with graph-aware `review` artifact.

- Khi archive reads graph state but graph state is unavailable
- Thì documented bridge behavior still blocks failing review metadata.

## Error Cases

- Khi docs imply `review: done` means accepted/pass
- Thì docs are unsafe and fail this test.

- Khi docs or active skill guidance route directly from `/apply` to `/archive`
- Thì regression exists.

- Khi `/archive` no longer blocks missing/failing/stale `review.md`
- Thì phase 1 gate regressed.

## Expected Outcomes

- Docs, specs, schema, and skills agree on graph-aware review.
- Phase 1 quality gate remains intact.
- Phase 2 adds OpenSpec graph awareness without weakening archive acceptance.

## Notes for Implementation

- Search active docs/skills separately from archived historical artifacts.
- Explicitly verify both graph behavior and metadata gate behavior.
