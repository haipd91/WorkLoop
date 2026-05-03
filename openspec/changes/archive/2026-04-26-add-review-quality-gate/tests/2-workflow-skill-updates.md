# Test Scenario: 2 workflow skill updates

- Source tasks:
  - 2.1 Update `/apply` guidance
  - 2.2 Add `/review` skill guidance
  - 2.3 Update `/archive` guidance
  - 2.4 Update active workflow docs
- Related requirements:
  - Hand off to /review after implementation
  - Require post-apply review artifact
  - Capture review outcome
  - Preserve human archive acceptance
- Assumptions:
  - `/review` is a Codex skill at `.codex/skills/review/SKILL.md`.
  - Workflow docs include active user-facing command examples.

## Happy Path

- Khi `/apply` completes all implementation tasks
- Thì final output routes user to `/review <name>`
- And it does not say the change is ready to archive.

- Khi user runs `/review <name>`
- Thì the skill reads proposal, specs, design, tasks, tests when present, plus implementation diff/worktree context.
- And it writes `openspec/changes/<name>/review.md`.

- Khi workflow docs show the full lifecycle
- Thì active command flow reads `/apply -> /review -> /archive`.

## Edge Cases

- Khi `/apply` stops with unfinished tasks
- Thì final output records remaining work and does not suggest archive.

- Khi tests are absent
- Thì `/review` records missing tests or skipped verification as residual risk instead of hiding it.

- Khi docs mention `/apply` in upstream flows such as `/tdd -> /apply`
- Thì that remains valid, but docs must not imply `/archive` comes directly after `/apply`.

## Error Cases

- Khi `.codex/skills/review/SKILL.md` is missing after implementation
- Thì workflow is incomplete because `/archive` depends on `review.md` produced by `/review`.

- Khi `/archive` guidance still says review failures are warnings
- Thì implementation fails the quality gate requirement.

- Khi active docs still show `/apply -> /archive`
- Thì implementation fails workflow alignment.

## Expected Outcomes

- `/apply` owns implementation only.
- `/review` owns review artifact generation.
- `/archive` owns final gate enforcement.
- User-facing docs and skill handoffs agree on `/apply -> /review -> /archive`.

## Notes for Implementation

- Do not add review validation logic to `/apply`.
- Do not generate review during `/archive`.
