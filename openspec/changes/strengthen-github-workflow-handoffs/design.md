## Context

Current state:

- GitHub orchestration skills exist for issue creation, PR creation, review posting, review sync, comment handling, and merge.
- Core OpenSpec skills still hand off `/apply -> /review -> /archive`.
- Active specs do not include `github-workflow` or `github-orchestration-skills`, even though archived changes defined those contracts.

This creates a workflow gap: a change can satisfy local review and archive without durable GitHub PR or merge evidence.

## Goals / Non-Goals

**Goals:**

- Make GitHub-backed delivery the default handoff path after exploration.
- Route completed implementation through PR creation before local review.
- Route passed local review through review posting, PR review sync, comment handling, and merge before archive.
- Make archive fail closed when GitHub-backed work lacks PR or merge evidence.
- Preserve an explicit local-only escape hatch for small or private changes.

**Non-Goals:**

- Implement GitHub Actions, labels, templates, or project automation.
- Auto-sync OpenSpec task state to GitHub.
- Require GitHub for every trivial local-only note or experiment.
- Replace OpenSpec artifacts with GitHub issue or PR text.

## Decisions

### Decision: GitHub-backed is the default durable path

The standard workflow becomes:

```text
/explore
  -> /github:create-issue
  -> /brief or /propose
  -> /tdd
  -> /apply
  -> /github:create-pr
  -> /review
  -> /github:post-review
  -> /github:sync-review
  -> /github:address-comments if needed
  -> /github:merge
  -> /archive
```

Rationale: GitHub is the durable source of truth for issue intent, PR review state, merge evidence, and collaboration. OpenSpec remains the structured artifact layer.

Alternative considered: keep GitHub skills optional. Rejected because optional skills do not satisfy a source-of-truth contract.

### Decision: Core skills hand off, GitHub skills own GitHub work

Core OpenSpec skills should not absorb all GitHub behavior. They should point to the right GitHub orchestration skill at workflow boundaries:

- `/apply` complete -> `/github:create-pr` for GitHub-backed changes.
- `/review` pass -> `/github:post-review`, then `/github:sync-review`.
- `/archive` -> require merge evidence or explicit local-only bypass.

Rationale: this keeps core skills focused while preventing bypass at key handoff points.

Alternative considered: rewrite `/apply`, `/review`, and `/archive` to fully manage GitHub. Rejected because it duplicates the dedicated GitHub skill contracts.

### Decision: Local-only must be explicit

Small changes can skip GitHub only when the human explicitly accepts local-only work and the handoff/review/archive records the missing GitHub evidence as accepted residual risk.

Rationale: not every change deserves full GitHub ceremony, but implicit bypass is the failure mode this change is fixing.

Alternative considered: require GitHub for every change. Rejected because it adds friction for harmless local-only maintenance.

### Decision: Archive is the final enforcement point

Archive must continue to parse `review.md` metadata, then add GitHub evidence checks for GitHub-backed changes.

Rationale: `/archive` is the last durable state transition. If it accepts missing PR or merge evidence silently, earlier handoffs become advisory only.

Alternative considered: rely on `/github:merge` to hand off correctly. Rejected because users can still call `/archive` directly.

## Risks / Trade-offs

- [Risk] Extra workflow friction for tiny changes -> Mitigation: allow explicit local-only bypass with documented residual risk.
- [Risk] GitHub evidence detection may be ambiguous -> Mitigation: block and ask for PR/issue link instead of guessing.
- [Risk] Core and GitHub skills drift again -> Mitigation: active specs must include both `github-workflow` and `github-orchestration-skills`.
- [Risk] Existing archived changes lack PR evidence -> Mitigation: apply the stricter gate to active GitHub-backed changes and allow documented legacy/local bypass.

## Migration Plan

1. Add active GitHub workflow specs.
2. Update core skill handoff text and schema done instructions.
3. Update archive guidance to require GitHub delivery evidence for GitHub-backed changes.
4. Verify active docs and skills no longer route directly from `/review` pass to `/archive` for GitHub-backed changes.

Rollback: revert the skill/spec updates. Existing OpenSpec review gate remains intact.

## Open Questions

- How should a change declare `GitHub-backed` versus `local-only`: issue link in artifacts, PR link in handoff, or explicit metadata?
- Should `/propose` suggest `/github:create-issue` when no issue exists, or should that remain an `/explore` outcome?
- Should archive verify merge evidence through GitHub MCP/plugin, local git history, PR URL in artifacts, or a combination?
