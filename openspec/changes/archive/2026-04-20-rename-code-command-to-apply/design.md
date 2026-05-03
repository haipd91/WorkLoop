## Context

The current workflow exposes `/code` as the public implementation entrypoint even
though the action is to apply approved change artifacts to a workflow. That mismatch
leaks into command names, skill prompts, workflow docs, and OpenSpec capability
specs, which makes the implementation phase sound like code generation instead of
change application.

This change is a cross-cutting contract rename, not a logic redesign. The public
command must become `/apply`, `/code` must stop being supported as a user-facing
command, and the implementation flow must continue to consume the same change
artifacts (`proposal.md`, `design.md`, `tasks.md`, and optional `tests/`).

Relevant evidence from the current repo:
- `docs/team-ai-workflow.md` still presents `/code` as the implementation tool and
  mentions `opsx:apply` as an alias
- `.codex/skills/` and `.claude/skills/` both contain prompts that tell users to
  run `/code`
- `openspec/specs/code-skill/spec.md`, `design-skill/spec.md`, and
  `tdd-skill/spec.md` still define implementation handoff in terms of `/code`
- `openspec/config.yaml` still says generated tests should remain useful context
  for `/code`

## Goals / Non-Goals

**Goals:**
- Make `/apply` the only supported public implementation command
- Preserve current implementation behavior and artifact-loading semantics under
  `/apply`
- Align workflow docs, skill prompts, and OpenSpec specs to the `/apply` term
- Remove active user-facing references that imply `/code` is still supported

**Non-Goals:**
- Refactor implementation logic beyond the minimum needed to expose `/apply`
- Rename unrelated internal symbols just for cosmetic consistency
- Keep a deprecated compatibility alias from `/code` to `/apply`
- Redesign the broader OpenSpec workflow outside the implementation step rename

## Proposed Architecture

Treat the change as a public workflow contract cutover with three layers:

1. Command surface layer
   - Replace the active implementation command exposure from `/code` to `/apply`
   - Remove `/code` from the supported user-facing command set instead of
     forwarding it

2. Workflow contract layer
   - Update the implementation contract so `/apply` is the documented entrypoint
     that reads approved artifacts and optional test context
   - Keep the artifact-loading model unchanged to avoid mixing rename work with
     behavior redesign

3. Documentation and mirrored skill layer
   - Update both `.codex/skills/` and `.claude/skills/` because the repo keeps
     parallel skill definitions
   - Update workflow docs and spec references so the review and handoff story is
     consistent end-to-end

No new subsystem is needed. The safe approach is to keep the execution path intact
and swap the user-facing contract at the edges where the workflow is exposed.

## Impact Map

- Command and invocation surface
  - Active implementation command registration or routing
  - Any help text or command discovery surface that lists `/code`

- Skill artifacts
  - `.codex/skills/code/SKILL.md`
  - `.codex/skills/design/SKILL.md`
  - `.codex/skills/propose/SKILL.md`
  - `.codex/skills/tdd/SKILL.md`
  - Mirrored files under `.claude/skills/`

- Workflow documentation
  - `docs/team-ai-workflow.md`

- OpenSpec contract artifacts
  - `openspec/specs/code-skill/spec.md`
  - `openspec/specs/design-skill/spec.md`
  - `openspec/specs/tdd-skill/spec.md`
  - `openspec/config.yaml` guidance that still references `/code`

- Change-specific artifacts for this rename
  - `openspec/changes/rename-code-command-to-apply/specs/**/*.md`
  - `openspec/changes/rename-code-command-to-apply/tasks.md`

## Decisions

### 1. Hard cutover from `/code` to `/apply`

`/apply` becomes the only supported user-facing implementation command. `/code`
is removed from the public workflow instead of being kept as an alias.

Why:
- This matches the accepted constraint that `code` should disappear from the
  public domain language
- Keeping `/code` alive would preserve the exact ambiguity the change is trying to
  remove

Rejected alternative:
- Keep `/code` as deprecated redirect: rejected because it prolongs the wrong
  mental model and weakens the rename

### 2. Preserve implementation behavior, rename the entrypoint

The change keeps the current implementation flow intact: `/apply` should read the
same change artifacts and optional `tests/` context that `/code` previously used.

Why:
- This keeps scope bounded
- It isolates regressions to command exposure and workflow text instead of mixing
  them with logic changes

Rejected alternative:
- Rework implementation behavior during rename: rejected because it expands blast
  radius without helping the naming problem

### 3. Update mirrored workflow surfaces together

The rename must update `.codex`, `.claude`, docs, and OpenSpec contract files in
the same change.

Why:
- The repo already stores duplicated workflow guidance
- Updating only one copy would create immediate drift and conflicting user
  instructions

Rejected alternative:
- Update only the currently used runtime surface: rejected because the repo itself
  would remain internally inconsistent

### 4. Fail closed if `/code` is still invoked

If any command registration path still exposes `/code`, it should be removed.
The system should not silently translate `/code` to `/apply`.

Why:
- Silent compatibility violates the agreed constraint
- It hides incomplete migration work

Rejected alternative:
- Hidden forwarding for convenience: rejected because it makes verification of the
  rename unreliable

## Risks / Trade-offs

- [Missed active reference] -> Mitigation: search `.codex`, `.claude`, `docs/`,
  `openspec/specs/`, and config files for user-facing `/code` before closing the
  implementation work
- [Command routing is outside the obvious skill files] -> Mitigation: find the
  authoritative command registration first, then update docs only after that path
  is confirmed
- [Users still expect `/code`] -> Mitigation: make `/apply` behavior equivalent to
  the old implementation flow so the rename changes naming, not the workflow
  mechanics
- [Over-scoping into internal refactors] -> Mitigation: rename internal code only
  where required to expose the new public command cleanly
- [Spec drift after implementation] -> Mitigation: update OpenSpec specs and repo
  docs in the same implementation session rather than as cleanup later

## Open Questions

- Where is the authoritative runtime registration for the current `/code`
  command in this repo or its host environment?
- Is `opsx:apply` still an active supported surface that must also be removed from
  docs, or is it only legacy wording in `docs/team-ai-workflow.md`?
- Are there any external personal automations outside this repo that still invoke
  `/code` and need a separate manual update?

## Review Notes

- Refined this file from a generic design note into a review-oriented handoff with
  explicit architecture, impact, and decision sections
- Added concrete impact mapping to repo areas that currently expose `/code`
- Tightened the contract decision: no deprecated alias, no silent forwarding
- Preserved the earlier scope decision that this is a rename/cutover, not a deep
  implementation refactor
