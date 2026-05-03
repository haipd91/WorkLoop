## Context

WorkLoop's core workflow is OpenSpec-driven: `/brief`, `/propose`, `/tdd`, `/apply`, `/review`, and `/archive` create durable planning, implementation, test, and review artifacts. The GitHub source-of-truth contract defines GitHub as the durable issue/PR/review/merge surface, but there is no practical skill layer that performs GitHub operations and hands the human to the next workflow step.

The design goal is to add a GitHub orchestration layer around the existing OpenSpec workflow. Core OpenSpec skills stay focused on OpenSpec artifacts; new GitHub skills own GitHub issue/PR/review/comment/merge operations.

## Goals / Non-Goals

**Goals:**

- Add six GitHub orchestration skills with clear responsibilities.
- Require MCP GitHub/plugin usage for GitHub operations.
- Define read-only versus mutating skill boundaries.
- Define a shared handoff output contract.
- Support PR review sync and comment addressing for inline file comments and general comments.
- Document the end-to-end GitHub + OpenSpec workflow.

**Non-Goals:**

- Rewrite core OpenSpec skills.
- Add `/github:link-work`.
- Add GitHub Actions enforcement.
- Add issue/PR templates.
- Add GitHub Projects bidirectional sync.
- Implement custom GitHub HTTP clients or token handling in chat.

## Decisions

### Decision: Add orchestration skills instead of rewriting core skills

New GitHub skills will sit before, between, and after OpenSpec skills. The core skills continue to own OpenSpec artifacts and do not become responsible for GitHub state.

Alternative considered: Modify every core skill to understand GitHub. Rejected because it couples issue/PR state into every OpenSpec phase and makes the workflow harder to review and evolve.

### Decision: Use action-specific GitHub skill names

The initial skill set will be:

- `/github:create-issue`
- `/github:create-pr`
- `/github:post-review`
- `/github:sync-review`
- `/github:address-comments`
- `/github:merge`

Alternative considered: Use abstract names such as `/github:intake` or `/github:link-work`. Rejected because action-specific names are clearer and avoid ceremony that does not directly solve a user task.

### Decision: Keep `/github:sync-review` read-only

`/github:sync-review` only reads PR review state, comments, requested changes, and checks, then outputs an action plan. It must not edit files, reply to comments, push commits, or mutate GitHub.

Alternative considered: Let sync also fix and reply. Rejected because mixing read and write behavior makes review state harder to audit and increases the risk of acting on ambiguous comments.

### Decision: Mutating skills must produce explicit handoff

Skills that create issues, create/update PRs, post review, address comments, or merge PRs must state what changed, what remains, what human action is needed, and what command should run next.

Alternative considered: Let each skill summarize freely. Rejected because human workflow state is the main value of this orchestration layer; inconsistent handoff would recreate chat-driven ambiguity.

### Decision: MCP GitHub/plugin is the only GitHub operation path

Skills must use MCP GitHub/plugin capabilities for GitHub reads/writes. They must not ask for secrets in chat or implement direct HTTP GitHub clients.

Alternative considered: Allow raw token or HTTP fallback. Rejected because it spreads secret handling and permission behavior across local skill instructions.

### Decision: Comment handling must distinguish inline and general comments

`/github:sync-review` and `/github:address-comments` must account for both inline file comments and general PR/review comments. General comments can produce tasks without file locations; inline comments should map to files/lines when possible.

Alternative considered: Only handle inline comments. Rejected because GitHub review feedback often includes high-level review comments that are actionable but not attached to a file.

## Risks / Trade-offs

- Skill count grows -> Mitigation: keep names action-specific and responsibilities narrow.
- MCP GitHub capability varies by environment -> Mitigation: blocked states must hand off missing connector/permission clearly.
- Mutating skills could push unintended changes -> Mitigation: mutating skills must inspect scope, run verification when applicable, and stop on ambiguity.
- Comment replies may not be supported equally for every comment type -> Mitigation: skill instructions should reply when supported and otherwise include a handoff note with unresolved reply action.
- Workflow may feel heavier for trivial work -> Mitigation: GitHub orchestration is opt-in around OpenSpec, not a rewrite of every core skill.

## Migration Plan

1. Add skill instruction files for the six GitHub orchestration skills.
2. Add/update workflow documentation showing where each skill fits around OpenSpec.
3. Keep core skills unchanged unless a minimal doc link is required.
4. Use scenario tests to validate skill contracts and handoff behavior.

Rollback is straightforward: remove the new GitHub skill files and docs links. No runtime service or schema migration is introduced.

## Open Questions

- Exact MCP GitHub tool names may depend on the active GitHub plugin surface at implementation time.
- Whether `/github:address-comments` should commit automatically or stop before commit may need implementation-time confirmation based on existing GitHub plugin capabilities.
- Whether `/github:merge` should support squash, merge commit, and rebase options in the first iteration or default to the repository's GitHub setting.
