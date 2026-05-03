## Context

The current workflow can move from `/apply` to `/archive` without a mandatory review record. Existing artifacts cover proposal, design, specs, tasks, and tests, but there is no post-implementation quality gate that records whether the implementation still matches the approved intent and whether remaining risk is acceptable.

This change adds a workflow gate, not application runtime behavior. The implementation should stay small and focus on skill/workflow documentation plus archive enforcement.

Current active workflow surfaces:

- `.codex/skills/apply/SKILL.md` currently routes completed implementation toward archive.
- `.codex/skills/archive/SKILL.md` currently treats incomplete artifacts/tasks as warnings and says not to block archive on warnings.
- `.codex/skills/review/SKILL.md` does not exist yet.
- `docs/ai-native-codex-workflow.md` contains active examples that go from `/apply` to `/archive`.

## Goals / Non-Goals

**Goals:**

- Require a post-apply `/review` step before archive.
- Store review results as a durable change-local artifact.
- Make archive fail closed when review evidence is missing, malformed, stale, or not passing.
- Guide `/apply` users toward `/review` as the next step after implementation.
- Keep the review gate deterministic enough for `/archive` to evaluate without new dependencies.

**Non-Goals:**

- Build a CI system.
- Require a human reviewer or external review service.
- Refactor the entire OpenSpec workflow.
- Change application runtime logic outside workflow/skill behavior.
- Add `review.md` to the OpenSpec artifact graph in this change.

## Proposed Architecture

```text
/apply
  |
  | completes implementation tasks
  v
/review
  |
  | writes openspec/changes/<name>/review.md
  v
/archive
  |
  | checks review gate before moving change
  v
openspec/changes/archive/YYYY-MM-DD-<name>/
```

`/review` is a new peer workflow skill. It owns review analysis and writes `openspec/changes/<name>/review.md`.

`/archive` consumes `review.md` as a hard pre-archive gate. Review failures are not warnings. They block archive before the change directory is moved.

`/apply` stays implementation-focused. It does not validate review state. It only changes its completion handoff so completed changes route to `/review`, not `/archive`.

`review.md` remains a skill-level artifact checked by `/archive`. It is not added to `openspec status` or schema artifact dependencies in this change. This avoids expanding scope into OpenSpec schema changes while still giving archive deterministic evidence to inspect.

## Impact Map

- `.codex/skills/apply/SKILL.md`
  - Replace all complete/all-done handoffs from archive to `/review`.
  - If implementation is paused or incomplete, do not suggest archive.

- `.codex/skills/review/SKILL.md`
  - New skill.
  - Reads proposal, specs, design, tasks, tests when present, plus implementation diff/worktree context.
  - Writes `openspec/changes/<name>/review.md`.

- `.codex/skills/archive/SKILL.md`
  - Adds hard review gate before archive mutation.
  - Blocks missing, malformed, stale, non-pass, or blocking review results.
  - Keeps existing artifact/task warnings after the review gate, but those warnings remain separate from review blockers.

- `docs/ai-native-codex-workflow.md`
  - Update active command sequences from `/apply -> /archive` to `/apply -> /review -> /archive`.
  - Add `/review` to the workflow explanation, Definition of Done, and anti-patterns.

- `openspec/changes/<name>/review.md`
  - New per-change artifact.
  - Durable handoff contract between `/review` and `/archive`.

- `openspec/specs/code-skill/spec.md`
  - Updated after archive sync to reflect `/apply` handoff to `/review`.

- `openspec/specs/review-quality-gate/spec.md`
  - New capability after archive sync.

## Decisions

1. Add `/review` as a dedicated peer skill.
   - Rationale: Preserves single responsibility: `/apply` implements, `/review` evaluates, `/archive` finalizes.
   - Alternatives considered:
     - Put review generation in `/apply`: rejected because implementation and acceptance would be coupled.
     - Put review generation in `/archive`: rejected because archive needs evidence before finalization, not review work during finalization.

2. Use `openspec/changes/<name>/review.md` as the review artifact.
   - Rationale: It matches the artifact-driven workflow and keeps review evidence beside proposal, design, tasks, specs, and tests.
   - Alternative considered: store review only in chat. Rejected because archive needs durable evidence.

3. Keep `review.md` outside the OpenSpec artifact graph for this change.
   - Rationale: Archive can enforce the gate directly by reading a stable file path. Adding schema/status support would widen scope.
   - Alternative considered: add `review.md` to schema artifacts. Rejected for now because it changes OpenSpec graph behavior and is not required for the quality gate.

4. Define a deterministic `review.md` contract.
   - Required top-level machine-checkable block:

     ```yaml
     verdict: pass | fail
     blocking_findings: none | present
     reviewed_ref: <git HEAD>
     reviewed_diff: <short diff fingerprint>
     reviewed_at: <ISO-8601 timestamp>
     ```

   - Required human-readable sections:
     - `## Findings`
     - `## Tests Run`
     - `## Residual Risk`
     - `## Verdict`
   - Archive accepts only `verdict: pass` and `blocking_findings: none`.
   - Missing, malformed, duplicate, contradictory, or unknown values fail closed.
   - Alternatives considered:
     - Free-form Markdown: rejected because archive parsing would be brittle.
     - Separate JSON artifact: rejected because Markdown is enough if the machine block is strict.

5. Add stale review protection.
   - `/review` records `reviewed_ref` and `reviewed_diff`.
   - `/archive` compares the current state against the recorded review state when possible.
   - If the current implementation changed after review, archive blocks and asks the user to rerun `/review`.
   - Rationale: A pass verdict is only meaningful for the implementation that was reviewed.
   - Alternative considered: check only review presence and verdict. Rejected because code can change after review.

6. Treat review gate failures as hard blockers.
   - Blocks:
     - missing `review.md`
     - malformed `review.md`
     - stale review
     - `verdict` not `pass`
     - `blocking_findings` not `none`
   - Rationale: Warning-and-confirm behavior preserves the original gap.

7. Use explicit legacy bypass only when needed.
   - Default: all active changes being archived after this gate lands require `review.md`.
   - Exception: a maintainer may create a documented bypass in `review.md` only for pre-gate legacy changes.
   - Bypass still requires a machine block with `verdict: pass`, `blocking_findings: none`, and a Residual Risk section explaining the bypass reason.
   - Rationale: Implicit bypass weakens the gate and makes archive behavior unpredictable.

8. Keep human acceptance separate from reviewer identity.
   - `/review` may be AI-authored.
   - `/archive` still presents the review summary and residual risk before finalization, preserving human risk acceptance at the archive step.
   - Rationale: The change does not require a human reviewer, but final archive remains a human-owned acceptance decision.

## Risks / Trade-offs

- [Risk] `review.md` becomes checkbox evidence -> Mitigation: require strict machine block plus findings/tests/residual-risk sections.
- [Risk] Archive accepts stale review after code changes -> Mitigation: record reviewed ref/diff and block when current state differs.
- [Risk] Archive behavior gets stricter for existing active changes -> Mitigation: default to retroactive `/review`; allow explicit documented bypass only for pre-gate legacy changes.
- [Risk] Review gate parsing becomes too complex -> Mitigation: use exact values and no new dependencies.
- [Risk] Docs drift leaves stale `/apply -> /archive` guidance -> Mitigation: update active workflow docs and skill handoffs in the same implementation.
- [Risk] `review.md` not in `openspec status` surprises users -> Mitigation: document that review is an archive-enforced gate, not part of the current artifact graph.

## Open Questions

- Exact diff fingerprint command can be chosen during implementation. It must be deterministic, dependency-free, and cheap.
- Whether a future change should add `review.md` to the OpenSpec artifact graph remains out of scope.

## Review Notes

Specialist analysis used:

- Architecture: confirmed `review.md` fits the artifact-driven architecture; warned against coupling review logic into `/apply`.
- Impact: identified primary files: `.codex/skills/apply/SKILL.md`, `.codex/skills/archive/SKILL.md`, new `.codex/skills/review/SKILL.md`, and `docs/ai-native-codex-workflow.md`.
- Failure Modes: flagged weak parsing, stale reviews, unclear applied-state detection, legacy bypass ambiguity, and skipped tests as core failure modes.
- Data Flow: modeled state as applied -> reviewed -> archivable, with `review.md` as the handoff contract.
- Review and Docs: flagged current docs that still route `/apply` directly to `/archive` and archive wording that treats warnings as non-blocking.

Design refinements from review:

- Chose dedicated `/review` skill.
- Chose `review.md` as the durable artifact.
- Chose strict machine-checkable review metadata.
- Chose stale review protection.
- Chose explicit legacy bypass policy.
- Chose archive hard-block behavior for review failures.
