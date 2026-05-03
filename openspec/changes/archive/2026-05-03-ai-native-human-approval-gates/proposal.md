## Why

The GitHub-backed workflow still makes the human operate mechanical gates such as draft readiness, approval interpretation, review artifact commits, merge readiness, and archive handoff. It also has a stale-review loop: committing `review.md` changes HEAD and can invalidate the review metadata even when implementation state is unchanged.

## What Changes

- Define an AI-native workflow where Codex runs operational steps and the human approves only material risk or release decisions.
- Update review metadata semantics so committing `review.md` does not invalidate the reviewed implementation state.
- Define when draft PRs can be marked ready automatically.
- Clarify GitHub approval versus human release approval.
- Treat missing CI/check data as N/A when no required checks are configured or exposed.
- Update merge/archive gate behavior to block only on meaningful safety failures.

## Capabilities

### New Capabilities

### Modified Capabilities

- `github-workflow`: Human approval boundary and AI-native delivery ownership.
- `github-orchestration-skills`: `/github:merge` readiness, draft, approval, and check semantics.
- `review-quality-gate`: Review artifact metadata and stale-review semantics.

## Impact

- Affected skill instructions: `.codex/skills/review/SKILL.md`, `.codex/skills/archive/SKILL.md`, `.codex/skills/github-merge/SKILL.md`, and possibly `.codex/skills/github-sync-review/SKILL.md`.
- Affected specs: `github-workflow`, `github-orchestration-skills`, `review-quality-gate`.
- No new external dependency expected.
