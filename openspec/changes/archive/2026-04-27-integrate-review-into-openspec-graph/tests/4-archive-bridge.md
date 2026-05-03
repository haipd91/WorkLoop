# Test Scenario: 4 archive bridge

- Source tasks:
  - 4.1 Update archive guidance to read graph state when available
  - 4.2 Preserve phase 1 metadata parsing
  - 4.3 Document fallback behavior
  - 4.4 Verify archive does not treat `review: done` as verdict pass
- Related requirements:
  - Use OpenSpec review artifact state in archive flow
  - Preserve phase 1 review metadata gate
- Assumptions:
  - Archive remains final acceptance boundary.
  - Review graph state and review verdict state are separate.

## Happy Path

- Khi archive starts
- And OpenSpec status reports `review` done
- And `review.md` metadata has pass verdict, no blocking findings, and current reviewed state
- Thì archive may continue to remaining checks.

## Edge Cases

- Khi OpenSpec status cannot expose review state
- Thì archive uses direct `review.md` metadata gate as documented bridge.

- Khi `review: done` is true but residual risk exists
- Thì archive still presents residual risk before final action.

## Error Cases

- Khi OpenSpec status reports `review` missing, blocked, or not done
- Thì archive blocks and instructs user to run `/review`.

- Khi status reports `review` done but metadata is malformed
- Thì archive blocks.

- Khi status reports `review` done but verdict is not pass
- Thì archive blocks.

- Khi status reports `review` done but blocking findings remain
- Thì archive blocks.

- Khi status reports `review` done but review is stale
- Thì archive blocks.

## Expected Outcomes

- Graph state is used for artifact presence/readiness.
- Metadata gate remains authoritative for pass/fail/stale.
- No path treats `review.md` existence as enough to archive.

## Notes for Implementation

- Archive should consume `openspec status --json` if reliable.
- Keep direct file parsing as bridge/fallback.
