# Test Scenario: 1 review gate definition

- Source tasks:
  - 1.1 Define the required `review.md` machine metadata
  - 1.2 Define the required `review.md` sections
  - 1.3 Define accepted metadata values
  - 1.4 Define fail-closed behavior for invalid metadata
  - 1.5 Define stale review behavior
  - 1.6 Define explicit documented legacy bypass behavior
  - 1.7 Document `/review` comparison inputs
- Related requirements:
  - Require post-apply review artifact
  - Define parseable review artifact contract
  - Block archive on stale review
  - Review implementation against approved artifacts
  - Allow only explicit documented legacy bypass
- Assumptions:
  - `review.md` is a skill-level artifact checked by `/archive`, not an OpenSpec artifact graph node.
  - `/archive` can parse exact metadata values without new dependencies.

## Happy Path

- Khi `/review` completes for an applied change
- Thì it writes `openspec/changes/<name>/review.md`
- And `review.md` contains machine metadata:
  - `verdict: pass`
  - `blocking_findings: none`
  - `reviewed_ref`
  - `reviewed_diff`
  - `reviewed_at`
- And `review.md` contains sections:
  - `## Findings`
  - `## Tests Run`
  - `## Residual Risk`
  - `## Verdict`
- And `/review` compares implementation against proposal, specs, design, tasks, and tests when present.

## Edge Cases

- Khi no findings exist
- Thì `review.md` explicitly records no findings and still records tests run, residual risk, and verdict.

- Khi optional planning artifacts are missing
- Thì `/review` uses available artifacts and records missing context under residual risk.

- Khi a pre-gate legacy change needs bypass
- Thì `review.md` documents the bypass reason and still uses passing machine metadata.

## Error Cases

- Khi `review.md` is missing required metadata
- Thì `/archive` must treat it as malformed and block archive.

- Khi `review.md` has duplicate or contradictory metadata
- Thì `/archive` must fail closed.

- Khi metadata contains unknown values such as `verdict: passed` or `blocking_findings: no`
- Thì `/archive` must fail closed.

- Khi current implementation state differs from `reviewed_ref` or `reviewed_diff`
- Thì `/archive` blocks as stale review and tells user to rerun `/review`.

## Expected Outcomes

- Review artifact format is deterministic enough for `/archive` to parse.
- Archive only accepts `verdict: pass` and `blocking_findings: none`.
- Stale, missing, malformed, or ambiguous review evidence cannot archive.

## Notes for Implementation

- Keep metadata values exact and case-sensitive unless implementation explicitly normalizes them.
- Prefer simple parser logic over broad Markdown interpretation.
