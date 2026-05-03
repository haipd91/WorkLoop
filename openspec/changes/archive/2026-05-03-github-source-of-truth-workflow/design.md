## Context

WorkLoop already uses OpenSpec artifacts to keep delivery artifact-driven instead of chat-driven. The missing piece is a documented contract for how GitHub Issues, branches, commits, and Pull Requests relate to those artifacts when GitHub is the repository-level source of truth.

This change is intentionally docs/spec only. It defines the operating model future skill and automation work can implement, without changing current command behavior or GitHub configuration.

## Goals / Non-Goals

**Goals:**

- Define GitHub as the durable source of truth for WorkLoop delivery state.
- Preserve OpenSpec as the structured planning, specification, task, test, and review artifact system.
- Define ownership boundaries between Issues, PRs, commits, branches, and OpenSpec files.
- Define naming conventions and quality gates that are concrete enough for future skill behavior and automation.

**Non-Goals:**

- Implement GitHub automation.
- Modify existing skill behavior.
- Add or change GitHub Actions.
- Add or migrate issue or PR templates.
- Auto-sync OpenSpec task state with GitHub comments, labels, or Projects.

## Decisions

### Decision: GitHub owns durable delivery state

GitHub Issues and Pull Requests will be documented as the primary source of truth for backlog, work intent, review, and delivery evidence. OpenSpec artifacts remain source-controlled files that make the work reviewable and auditable inside the repo.

Alternative considered: Keep OpenSpec as the only source of truth and treat GitHub as transport. Rejected because the target workflow needs issue tracking and PR review to be first-class, durable, and visible outside local chat/session context.

### Decision: OpenSpec owns structured reasoning artifacts

OpenSpec will own proposal, specs, design, tasks, tests, and review artifacts. GitHub will link to those files rather than duplicating all structured content in issue or PR comments.

Alternative considered: Copy all OpenSpec details into GitHub issue/PR bodies. Rejected because it creates drift and noisy updates; durable details should live once in committed files.

### Decision: Start with a contract, not automation

This change will only add docs/spec artifacts. Automation, skill behavior, GitHub Actions, and templates are future changes that can reference this contract.

Alternative considered: Implement the full GitHub workflow now. Rejected because it would combine policy, skill behavior, CI, and template changes into one large review surface.

### Decision: Quality gates must reference durable artifacts

The workflow will require material decisions, review readiness, and archive readiness to be backed by GitHub or committed OpenSpec artifacts. Chat-only decisions are not sufficient.

Alternative considered: Allow Codex session context to satisfy readiness. Rejected because sessions are not durable enough for repo-level source of truth.

## Risks / Trade-offs

- Policy without enforcement -> Mitigation: keep requirements concrete and create follow-up changes for skill behavior or automation only after this contract is accepted.
- Duplicate state between GitHub and OpenSpec -> Mitigation: define ownership boundaries so GitHub owns tracking/review while OpenSpec owns structured artifacts.
- Too much process for small changes -> Mitigation: document that gates scale with change risk, while still requiring durable context for non-trivial work.
- Ambiguous future automation scope -> Mitigation: explicitly mark automation, Actions, templates, and sync behavior as out of scope here.

## Migration Plan

1. Add the `github-workflow` spec and supporting documentation/tasks in this change.
2. Review the workflow contract through OpenSpec and PR review.
3. After approval, create separate GitHub issues or OpenSpec changes for skill behavior, templates, automation, or GitHub Actions.

Rollback is simple: revert the docs/spec commit or archive rejection. No runtime behavior changes are introduced.

## Open Questions

- Which GitHub label set should be standardized first: issue lifecycle labels, PR review labels, or both?
- Should issue and PR templates be generated from this contract in a follow-up change?
- Should future skill changes require a GitHub issue for every OpenSpec change, or only non-trivial changes?
