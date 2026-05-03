# Test Scenario: 3 spec and regression alignment

- Source tasks:
  - 3.1 Update implementation-related specs and any generated workflow artifacts required by the new `/apply` contract
  - 3.2 Search the repository for stale user-facing `/code` references and remove or replace them when they are part of the active workflow
  - 3.3 Validate the end-to-end workflow handoff now reads as `/propose` -> `/design` or `/tdd` -> `/apply` without implying `/code` support
- Related requirements:
  - Read tests directory as implementation context
  - Preserve approved design as `/code` input
  - Human review and approve before handoff
  - Provide scenario-based test fallback
- Assumptions:
  - OpenSpec specs and config files are part of the active contract and must match the implemented workflow
  - Repository-wide search results need triage so archived references are not mistaken for active regressions

## Happy Path

- When the rename is complete
- Then the active OpenSpec specs describe implementation handoff in terms of `/apply`
- And config or guidance files no longer say generated tests are context for `/code`
- And the active workflow reads cleanly as proposal/design/tdd handoff into `/apply`

## Edge Cases

- Archived changes still contain `/code` because they record historical behavior rather than active workflow requirements
- Some references to `code` remain as internal identifiers, file paths, or change names that are not user-facing regressions
- The active config is updated, but one spec file still describes `/code`, causing contract drift

## Error Cases

- `openspec/specs/` and repo docs disagree about whether `/code` is still supported
- `openspec/config.yaml` still tells implementers that tests are context for `/code`
- The workflow appears renamed in one artifact set but still leaks `/code` in another active artifact, making implementation intent ambiguous

## Expected Outcomes

- Active specs, config, and workflow docs agree that `/apply` is the only user-facing implementation command
- Repository search can distinguish active regressions from archived historical mentions
- The rename change is safe to implement without hidden contract drift

## Notes for Implementation

- Review search results with judgment; not every `code` string is a bug
- Prioritize active specs, config, and user-facing workflow surfaces over archive cleanup
