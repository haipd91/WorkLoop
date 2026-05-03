## 1. Shared GitHub Skill Contracts

- [x] 1.1 Define the shared MCP GitHub/plugin usage rule and no-chat-secrets rule for all GitHub orchestration skills.
- [x] 1.2 Define the shared handoff output contract with current state, done, needs human, next command, blockers, and links.
- [x] 1.3 Define read-only versus mutating skill boundaries.

## 2. Issue and PR Skills

- [x] 2.1 Add `/github:create-issue` skill instructions for creating issues and handing off issue URL/number plus suggested OpenSpec change name.
- [x] 2.2 Add `/github:create-pr` skill instructions for creating or updating draft PRs with issue link, OpenSpec path, summary, verification, and residual risk.
- [x] 2.3 Add `/github:post-review` skill instructions for posting `review.md` verdict, findings, tests, and residual risk to a PR.

## 3. Review Feedback Skills

- [x] 3.1 Add `/github:sync-review` skill instructions as a read-only PR review/comment/check sync and action-plan generator.
- [x] 3.2 Add `/github:address-comments` skill instructions for handling inline file comments and general PR/review comments.
- [x] 3.3 Define ambiguity and scope-conflict stop conditions for `/github:address-comments`.

## 4. Merge Skill

- [x] 4.1 Add `/github:merge` skill instructions for checking review, CI, issue, and local `review.md` gates before merge.
- [x] 4.2 Define issue closing behavior and `/archive <change-name>` handoff after merge.

## 5. Workflow Documentation

- [x] 5.1 Add or update docs showing the end-to-end GitHub orchestration flow around OpenSpec.
- [x] 5.2 Document the distinction between core OpenSpec skills and GitHub orchestration skills.
- [x] 5.3 Document read-only versus mutating GitHub skills.
- [x] 5.4 Document handoff contract and example handoffs for successful and blocked states.
- [x] 5.5 Link existing GitHub source-of-truth workflow docs to the orchestration workflow.

## 6. Verification

- [x] 6.1 Run OpenSpec validation for the change and fix artifact issues.
- [x] 6.2 Review skill instructions against the GitHub orchestration spec requirements.
- [x] 6.3 Review docs against the brief acceptance criteria.
