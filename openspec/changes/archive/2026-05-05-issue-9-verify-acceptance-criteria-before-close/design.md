## Context

GitHub issues created through `/github:create-issue` include `Acceptance Criteria` checkboxes. The current GitHub-backed workflow has strong review, PR, sync, and merge gates, but `/github:merge` does not explicitly verify and reflect those criteria before merging or closing the linked issue.

This change is instruction/spec focused. It strengthens the durable delivery record without adding runtime automation, a GitHub Action, a custom parser library, or a new issue template.

Specialist review found one critical ordering decision: acceptance-criteria verification must run before PR merge, not merely before manual issue close. A PR merge can auto-close linked issues, so post-merge verification is too late to act as a real gate.

## Goals / Non-Goals

**Goals:**

- Make `/github:merge` verify linked issue acceptance criteria before PR merge and issue close.
- Require criterion-level evidence mapping before changing `- [ ]` to `- [x]`.
- Verify all criteria, including criteria already checked before merge time.
- Block merge when criteria are uncovered, ambiguous, unparseable, uneditable, or unsupported by durable bypass evidence.
- Preserve `/review` as the deep implementation review gate.

**Non-Goals:**

- Do not rerun a full implementation review inside `/github:merge`.
- Do not auto-check all criteria because PR checks, review metadata, or GitHub review state passed.
- Do not introduce a GitHub Action, custom GitHub client, parser dependency, or structured evidence database.
- Do not redesign issue templates beyond using the existing `## Acceptance Criteria` section.
- Do not update `/github:create-issue`, `/github:post-review`, or `/github:sync-review` unless implementation finds a direct contract gap.

## Proposed Architecture

`/github:merge` remains the only skill instruction that needs direct behavior change. It already owns GitHub PR/issue/review/check/merge/close operations and is the last gate before `/archive`.

The refined merge flow should be:

1. Resolve PR, repository, linked issue, and OpenSpec change.
2. Read PR review/comment/check state through GitHub MCP/plugin.
3. Read local `review.md` metadata and other available evidence sources.
4. Read the linked issue body and locate the `## Acceptance Criteria` section.
5. Parse task-list criteria under that section.
6. Build a criterion-level coverage map:
   - criterion text
   - current checkbox state
   - coverage status: `covered`, `uncovered`, `ambiguous`, or `bypassed`
   - evidence source: `review.md`, `tasks.md`, tests, verification output, PR note, issue/PR comment, or documented bypass
   - action taken: `checked`, `left checked`, `blocked`, or `bypassed`
7. Re-read the issue body immediately before mutation.
8. Update only checkbox markers for criteria with sufficient evidence.
9. Re-read the issue body after mutation and verify expected checkbox state.
10. If any criterion is uncovered, ambiguous, unparseable, uneditable, or checked without evidence, block before human release approval and before PR merge.
11. Continue existing reviewer approval, check, draft-ready, release approval, merge, issue close, and archive handoff gates only after AC closure verification passes.

Parseable criteria are markdown task-list items under the issue `## Acceptance Criteria` section. The implementation should preserve criterion text and mutate only the checkbox marker. If the section is missing, duplicated, nested ambiguously, inside quoted/code content, or otherwise unsafe to parse, `/github:merge` should fail closed and report exact ambiguity in the handoff.

Bypass is an evidence type, not a shortcut. It must be documented in a durable GitHub or committed OpenSpec artifact, preferably a PR or issue comment that includes the criterion text and reason. Chat-only bypass is not sufficient.

## Impact Map

- `.codex/skills/github-merge/SKILL.md`: primary implementation surface. Update gates, workflow order, blocked states, and handoff expectations.
- `openspec/specs/github-workflow/spec.md`: workflow-level behavior changes after archive: issue closure must reflect verified AC state.
- `openspec/specs/github-orchestration-skills/spec.md`: orchestration-level behavior changes after archive: `/github:merge` must read, parse, map, update, verify, and block.
- `openspec/specs/review-quality-gate/spec.md`: evidence source only. No metadata or archive-gate redesign.
- GitHub issue body: mutable durable intake record; AC checkbox state is read and updated.
- PR/review artifacts: durable evidence sources consumed by `/github:merge`.
- `/archive`: downstream consumer of merge/issue closure evidence; no direct change planned.

## Decisions

### Decision 1: AC verification runs before merge

`/github:merge` must complete acceptance-criteria evidence mapping and issue checkbox update before PR merge, human release approval, and issue close. This avoids merged-but-not-closable and auto-closed-with-stale-checkbox states.

Alternative considered: verify only before explicit issue close after merge. Rejected because GitHub may auto-close linked issues on merge.

### Decision 2: Verify all criteria, not only unchecked criteria

The gate must map evidence for checked and unchecked criteria. A pre-checked criterion without evidence is still a risk because the original problem includes mechanically checked boxes.

Alternative considered: trust already checked criteria. Rejected because it makes the new gate cosmetic.

### Decision 3: Use criterion-level evidence mapping

Each criterion needs a concrete evidence source. `review.md: pass`, completed tasks, or CI pass can support evidence, but none should blanket-satisfy every criterion without a per-criterion mapping.

Alternative considered: treat global pass signals as enough. Rejected because it recreates the blind-checkbox problem.

### Decision 4: Fail closed on parsing, edit, or linkage ambiguity

If `/github:merge` cannot determine the linked issue, parse the AC section, update the issue body, verify the post-update body, or map evidence cleanly, it must block with a handoff instead of merging or closing.

Alternative considered: merge PR and leave issue open. Rejected because it weakens the gate and creates partial delivery state.

### Decision 5: Keep scope to specs and merge skill instructions

No parser dependency, GitHub Action, issue template migration, or new evidence artifact is needed. The instruction-driven flow is enough for this workflow improvement.

## Risks / Trade-offs

- [Risk] Criteria are vague or multi-clause, making evidence mapping subjective. -> Mitigation: mark criterion `ambiguous`, block merge, and require clarification or durable bypass.
- [Risk] Issue body update overwrites concurrent edits. -> Mitigation: re-read immediately before and after update; preserve full body text; change only expected checkbox markers; block on drift or edit conflict.
- [Risk] A broad `review.md` pass is misused as blanket evidence. -> Mitigation: require per-criterion mapping with cited evidence.
- [Risk] Human bypass remains chat-only. -> Mitigation: require bypass in a durable PR/issue/OpenSpec artifact before continuing.
- [Risk] Multiple linked issues or conflicting issue references cause wrong issue verification. -> Mitigation: block on missing, multiple, or conflicting linked issue targets unless the human explicitly selects the target and records that selection durably.
- [Risk] Merge feels heavier. -> Mitigation: keep this as final closure verification only; do not rerun full implementation review.

## Open Questions

- Exact parse rules can stay instruction-level for this change, but `/apply` should decide whether to document accepted task-list forms in `.codex/skills/github-merge/SKILL.md` as top-level `- [ ]` / `- [x]` items under `## Acceptance Criteria`.
- Durable bypass location should prefer PR or issue comment. If GitHub comment write is unavailable, `/github:merge` should block unless another committed durable artifact is available.

## Review Notes

- Specialist agents used: Architecture, Impact, Failure Modes, Data Flow, Review and Docs.
- Core consensus: `/github:merge` is the correct ownership boundary; `/review` remains deep review; `review.md` is evidence source only.
- Design refinement changed the gate order from "before issue close" to stricter "before merge and issue close".
- Design refinement added handling for already checked criteria, auto-close, stale issue body, parse ambiguity, linked issue ambiguity, and durable bypass.
- Security and Performance agents were not run because the change does not touch auth, permissions, sensitive data, latency, throughput, or concurrency behavior beyond issue body edit race handling covered by Failure Modes and Data Flow.
