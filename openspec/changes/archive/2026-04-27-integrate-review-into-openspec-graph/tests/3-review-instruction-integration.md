# Test Scenario: 3 review instruction integration

- Source tasks:
  - 3.1 Verify status includes review
  - 3.2 Verify review blocked while prior artifacts missing
  - 3.3 Verify review ready when prior artifacts complete and review.md absent
  - 3.4 Verify review done when review.md exists
  - 3.5 Verify instructions review returns output/deps/context
  - 3.6 Add generic bridge if schema-only dispatch fails
- Related requirements:
  - Model review as an OpenSpec artifact
  - Provide review instructions from schema
  - Generic artifact dispatch is required
- Assumptions:
  - Adding a schema artifact may or may not be enough for current OpenSpec CLI dispatch.

## Happy Path

- Khi `openspec status --change <name>` runs
- Thì returned artifacts include `review`.

- Khi `openspec instructions review --change <name> --json` runs
- Thì response includes:
  - `outputPath: review.md`
  - dependencies for proposal, specs, design, tasks, tests
  - context files for review
  - schema-backed instruction text.

## Edge Cases

- Khi prior artifacts are incomplete
- Thì `review` is blocked and status names missing dependencies.

- Khi prior artifacts are complete and `review.md` is absent
- Thì `review` is ready.

- Khi `review.md` exists
- Thì `review` is done.

## Error Cases

- Khi `openspec instructions review` returns artifact-not-found after schema update
- Thì implementation must add minimal generic artifact dispatch bridge.

- Khi bridge is hardcoded only for `review`
- Thì future schema artifacts remain coupled to CLI code and design intent is not met.

## Expected Outcomes

- Review becomes visible and actionable through OpenSpec status/instructions.
- Any CLI bridge remains schema-driven and minimal.

## Notes for Implementation

- Prefer generic schema artifact dispatch over review-specific branching.
