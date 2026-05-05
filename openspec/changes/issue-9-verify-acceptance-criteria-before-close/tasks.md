## 1. Spec and Workflow Contract

- [x] 1.1 Verify `github-workflow` delta requires linked issue acceptance criteria verification before issue close.
- [x] 1.2 Verify `github-orchestration-skills` delta requires `/github:merge` to read, parse, and evaluate linked issue criteria before merge or close.
- [x] 1.3 Confirm specs distinguish acceptance-criteria closure verification from full `/review` implementation review.

## 2. Merge Skill Instructions

- [x] 2.1 Update `.codex/skills/github-merge/SKILL.md` gates to include acceptance-criteria coverage before linked issue close.
- [x] 2.2 Update `/github:merge` workflow steps to read issue body, map criteria to evidence, update checked criteria, and block uncovered criteria.
- [x] 2.3 Update `/github:merge` blocked states and handoff expectations for uncovered, ambiguous, unparseable, or uneditable acceptance criteria.

## 3. Verification

- [x] 3.1 Run OpenSpec validation for `issue-9-verify-acceptance-criteria-before-close`.
- [x] 3.2 Review generated tasks/tests coverage against the brief acceptance criteria.
- [x] 3.3 Confirm no new dependency, GitHub Action, or issue template migration was introduced.
