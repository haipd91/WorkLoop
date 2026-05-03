## 1. Active GitHub Specs

- [x] 1.1 Sync `github-workflow` into active `openspec/specs/github-workflow/spec.md`.
- [x] 1.2 Sync `github-orchestration-skills` into active `openspec/specs/github-orchestration-skills/spec.md`.
- [x] 1.3 Verify active specs describe `/explore -> /github:create-issue -> OpenSpec -> GitHub PR/review/merge -> /archive`.

## 2. Core Handoff Updates

- [x] 2.1 Update `/apply` handoff so GitHub-backed completed implementation routes to `/github:create-pr <change-name>` before `/review`.
- [x] 2.2 Update `/review` pass handoff so GitHub-backed changes route to `/github:post-review <pr>` and `/github:sync-review <pr>` before merge/archive.
- [x] 2.3 Update schema apply done instruction so generated OpenSpec apply guidance matches the GitHub-aware handoff.
- [x] 2.4 Preserve explicit local-only exception language for changes where the human accepts skipping GitHub.

## 3. Archive Gate Updates

- [x] 3.1 Update `/archive` guidance to require durable PR or merge evidence for GitHub-backed changes.
- [x] 3.2 Define how archive blocks when review passes but PR/merge evidence is missing.
- [x] 3.3 Preserve existing `review.md` metadata, stale diff, and blocking findings checks.
- [x] 3.4 Document local-only or legacy bypass handling with residual risk.

## 4. Verification

- [x] 4.1 Run OpenSpec validation for `strengthen-github-workflow-handoffs`.
- [x] 4.2 Search active skills and schema guidance for direct `/review` pass to `/archive` bypasses.
- [x] 4.3 Verify GitHub orchestration skills still own GitHub operations and core skills only hand off.
- [x] 4.4 Record any remaining ambiguity around GitHub-backed detection as residual risk for `/review`.
