# Test Scenario: 1 schema graph integration

- Source tasks:
  - 1.1 Add `review` artifact to schema with output path `review.md`
  - 1.2 Set `review` dependencies
  - 1.3 Keep `apply.requires` without review
  - 1.4 Add schema review instruction text
  - 1.5 Ensure `review: done` is not verdict pass
- Related requirements:
  - Model review as an OpenSpec artifact
  - Gate review readiness on prior artifacts
  - Keep review out of apply prerequisites
- Assumptions:
  - `spec-driven-custom` schema remains the source of artifact graph truth.
  - `review.md` remains a single deterministic root-level change artifact.

## Happy Path

- Khi `openspec/schemas/spec-driven-custom/schema.yaml` is updated
- Thì artifacts include `review`
- And `review.generates` is `review.md`
- And `review.requires` includes proposal, specs, design, tasks, and tests
- And `apply.requires` still includes only specs, tasks, and tests.

## Edge Cases

- Khi `review.md` exists
- Thì status may mark `review` done
- But that does not imply `verdict: pass`.

- Khi all prior artifacts are complete but `review.md` is absent
- Thì status marks `review` ready.

## Error Cases

- Khi `review` is added to `apply.requires`
- Thì apply can be blocked by a post-implementation artifact and the workflow is invalid.

- Khi `review: done` is treated as verdict pass
- Thì archive can bypass metadata checks and the phase 1 gate is weakened.

## Expected Outcomes

- `review` becomes a graph artifact.
- Review readiness depends on complete prior artifacts.
- Apply readiness remains independent of review.
- Graph state and quality verdict remain separate.

## Notes for Implementation

- Do not encode review verdict semantics in the schema graph.
- Keep verdict/stale/blocking checks in archive/review gate logic.
