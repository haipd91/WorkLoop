## Context

Phase 1 added `/review` as a durable quality gate and made `/archive` fail closed on missing, malformed, stale, non-pass, or blocking review state. That solved the immediate safety issue, but the OpenSpec schema still models only `proposal`, `specs`, `design`, `tasks`, and `tests`.

Current gap:

- `openspec status --change <name>` cannot show `review`.
- `openspec instructions review --change <name> --json` fails because `review` is not a schema artifact.
- `openspec instructions apply --change <name> --json` still emits generic all-done archive guidance.
- `/archive` enforces review via skill logic, not through graph-aware review artifact state.

## Goals / Non-Goals

**Goals:**

- Add `review` as a first-class artifact in `spec-driven-custom`.
- Make status/instructions understand `review.md` and its dependencies.
- Route apply all-done guidance to `/review`.
- Keep phase 1 review content, metadata contract, and archive fail-closed behavior intact.

**Non-Goals:**

- Change review stance or `review.md` metadata contract.
- Build CI or require a human reviewer.
- Refactor the entire OpenSpec CLI.
- Change application runtime behavior.
- Treat `review` artifact done as equivalent to review verdict pass.

## Proposed Architecture

```text
proposal/specs/design/tasks/tests
              |
              v
        review artifact
      generates review.md
              |
              v
       archive preflight
    graph state + metadata gate
```

Graph state and quality state stay separate:

- OpenSpec graph owns artifact identity, output path, dependencies, ready/blocked/done state, and schema-backed instructions.
- `/review` owns review analysis and writes `review.md`.
- `/archive` consumes graph state first, then parses `review.md` metadata for verdict, blocking findings, and stale state.

## Impact Map

- `openspec/schemas/spec-driven-custom/schema.yaml`
  - Add `review` artifact with `generates: review.md`.
  - Set `review.requires` to `proposal`, `specs`, `design`, `tasks`, and `tests`.
  - Add review instruction text and template.
  - Update apply instruction text so all-done implementation routes to `/review`.

- OpenSpec CLI behavior
  - `openspec status --change <name>` should include `review`.
  - `openspec instructions review --change <name> --json` should resolve from schema.
  - If generic artifact dispatch does not work after schema update, implementation should add the smallest schema-driven bridge, not hardcoded review-only branching.
  - In OpenSpec CLI 1.3.0, apply all-done guidance is hardcoded. The minimal bridge is `apply.doneInstruction` so schema can override only the all-done message.

- `.codex/skills/archive/SKILL.md`
  - Read OpenSpec review artifact state when available.
  - Continue phase 1 metadata parsing as authoritative pass/fail/stale gate.

- `.codex/skills/review/SKILL.md`
  - Keep stance and metadata contract unchanged.
  - May reference schema-backed instructions when available.

- `.codex/skills/apply/SKILL.md`
  - Stay aligned with schema apply all-done routing to `/review`.

- `docs/ai-native-codex-workflow.md`
  - Explain graph-aware review and clarify that status done means artifact exists, not verdict pass.

## Decisions

1. Add `review` after `tests` in `artifacts`.
   - Rationale: review runs after planning, test intent, and implementation task context exist.
   - Alternatives considered:
     - Keep review outside graph. Rejected for phase 2 because status/instructions must become graph-aware.
     - Add review before tests. Rejected because review should evaluate test intent and implementation against it.

2. Define `review` output as `review.md`.
   - Rationale: phase 1 already established `review.md` as durable evidence consumed by archive.
   - Alternative considered: `reviews/**/*`. Rejected because archive expects one deterministic file and status should remain simple.

3. Require proposal, specs, design, tasks, and tests before graph-backed review is ready.
   - Rationale: graph-backed review should be the standard complete-path gate for spec-driven-custom changes.
   - Trade-off: existing `/review` skill can still record missing artifacts as residual risk for legacy/manual paths, but schema-backed review readiness is stricter.
   - Alternative considered: allow partial graph-backed review. Rejected because it weakens the purpose of making review official.

4. Keep `review` out of `apply.requires`.
   - Rationale: review is post-implementation. Adding it to apply prerequisites would invert dependency direction.
   - Alternative considered: require review before apply. Rejected as circular and wrong.

5. Add schema-level review instructions.
   - Rationale: `openspec instructions review --change <name> --json` should return output path, dependencies, context, and review guidance from schema.
   - Alternative considered: keep review instructions only in `.codex/skills/review/SKILL.md`. Rejected because CLI would still not know review is official.

6. Update apply all-done instruction to route to `/review`.
   - Rationale: all-done implementation is not final acceptance.
   - Alternative considered: keep generic archive text and rely on `/apply` skill override. Rejected because it leaves CLI/source-of-truth drift.

7. Keep archive metadata validation as the authoritative quality gate.
   - Rationale: OpenSpec graph can say `review.md` exists, but cannot by itself prove verdict pass, no blocking findings, or freshness.
   - Alternative considered: treat graph `review: done` as pass. Rejected because artifact existence is weaker than the phase 1 gate.

## Risks / Trade-offs

- [Risk] OpenSpec CLI may not support arbitrary new artifact IDs from schema -> Mitigation: after schema change, verify `openspec instructions review`; if it fails, add a minimal generic artifact dispatch bridge.
- [Risk] The all-done apply instruction bridge patches the local OpenSpec CLI install outside this repo -> Mitigation: document the exact local patch and migration verification in `docs/openspec-local-cli-patch.md`.
- [Risk] Users may read `review: done` as review passed -> Mitigation: docs and archive wording must say graph done only means `review.md` exists; archive still checks metadata.
- [Risk] Strict graph dependencies may block review for legacy/partial changes -> Mitigation: standard spec-driven-custom path requires full artifacts; legacy/manual bypass remains documented in phase 1 review gate.
- [Risk] Schema instruction and `/review` skill drift -> Mitigation: schema instruction should describe artifact contract and context; `/review` skill remains detailed execution guide.
- [Risk] Archive consumes schema internals too tightly -> Mitigation: prefer `openspec status --json` as interface; only fall back to minimal documented bridge if CLI lacks support.

## Migration Plan

1. Update `openspec/schemas/spec-driven-custom/schema.yaml`:
   - add `review` artifact
   - set `generates: review.md`
   - set dependencies
   - add review instruction/template
   - add apply `doneInstruction` text
2. Verify OpenSpec behavior:
   - `openspec status --change <name>` includes `review`
   - `openspec instructions review --change <name> --json` resolves
   - `openspec instructions apply --change <name> --json` routes all-done to `/review`
3. Update `/archive` guidance to consume graph state when available and keep metadata parsing.
4. Update docs/specs.
5. Keep phase 1 behavior passing.

Rollback:

- Remove `review` artifact from schema.
- Restore apply instruction text.
- Keep phase 1 skill-level review gate.

## Open Questions

- Does adding a schema artifact automatically enable `openspec instructions review`, or does the CLI need a generic artifact dispatch bridge?
- Can `/archive` reliably consume `openspec status --json` for review graph state, or should it keep checking `review.md` directly as fallback?

## Review Notes

Specialist analysis:

- Architecture agent completed. Key finding: schema graph should own review artifact metadata, but verdict/stale enforcement must stay outside graph.
- Impact, Failure Modes, Data Flow, and Review Docs agents could not run due usage limit. Orchestrator performed local analysis from artifacts, schema, specs, and skills.

Design refinements from review:

- Explicitly separated graph state from quality gate state.
- Resolved strict graph-backed review readiness: full prior artifacts required.
- Kept partial-context review only as legacy/manual fallback behavior.
- Added minimal generic dispatch bridge as implementation fallback if schema-only integration is insufficient.
- Clarified that `review: done` does not mean verdict pass.
