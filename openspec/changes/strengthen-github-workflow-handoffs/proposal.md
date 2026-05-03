## Why

GitHub orchestration skills now exist, but the core OpenSpec handoffs still allow a completed change to move from `/review` directly to `/archive`. That weakens the GitHub source-of-truth workflow because PR creation, review posting, PR feedback sync, merge evidence, and issue linkage can be skipped.

## What Changes

- Strengthen core workflow handoffs so GitHub-backed changes route through GitHub orchestration steps before archive.
- Update post-apply and post-review guidance to include PR creation, review posting, review sync, comment handling, and merge gates.
- Update archive readiness requirements so GitHub-backed changes require durable PR or merge evidence before archive.
- Promote the GitHub workflow contract into active specs instead of leaving it only in archived change artifacts.
- Keep small local-only changes possible through explicit human acceptance when GitHub tracking is not needed.

## Capabilities

### New Capabilities

- `github-workflow`: Defines GitHub Issues, Pull Requests, branches, commits, and OpenSpec artifacts as the durable delivery workflow contract.
- `github-orchestration-skills`: Defines the GitHub skill set, handoff contract, and read-only versus mutating workflow boundaries.

### Modified Capabilities

- `code-skill`: Post-apply handoff changes from review-only guidance to GitHub-aware PR and review routing.
- `review-quality-gate`: Archive readiness changes to include GitHub PR or merge evidence for GitHub-backed changes.

## Impact

- Affected skill instructions: `.codex/skills/apply/SKILL.md`, `.codex/skills/review/SKILL.md`, `.codex/skills/archive/SKILL.md`, and possibly `.codex/skills/propose/SKILL.md`.
- Affected schema guidance: `openspec/schemas/spec-driven-custom/schema.yaml`.
- Affected specs: active `openspec/specs/` entries for GitHub workflow and existing workflow gates.
- No new runtime dependency expected.
