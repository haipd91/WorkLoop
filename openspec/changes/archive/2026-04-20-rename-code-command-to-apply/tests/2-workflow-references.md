# Test Scenario: 2 workflow references and mirrored skill updates

- Source tasks:
  - 2.1 Update `.codex/skills/` instructions and prompts that reference `/code` so they consistently point to `/apply`
  - 2.2 Update `.claude/skills/` instructions and prompts that reference `/code` so the mirrored skill set stays aligned
  - 2.3 Update workflow docs and help text, including `docs/team-ai-workflow.md`, to describe `/apply` as the only implementation command
- Related requirements:
  - Preserve approved design as `/code` input
  - Human review and approve before handoff
- Assumptions:
  - `.codex/skills/` and `.claude/skills/` are both maintained workflow surfaces in this repo
  - User-facing guidance should be fully consistent about the implementation command and handoff steps

## Happy Path

- When a user reads workflow docs or skill instructions
- Then every implementation handoff prompt points to `/apply`, not `/code`
- And `/design` and `/tdd` both hand off to `/apply` consistently
- And `docs/team-ai-workflow.md` no longer advertises `/code` or `opsx:apply` as active implementation commands

## Edge Cases

- Some historical or archived change artifacts still mention `/code`, but they are not part of the active workflow contract
- One mirrored skill tree is updated while the other is not, creating inconsistent prompts between environments
- A doc uses `code` in a purely descriptive sentence about internals rather than as a command reference

## Error Cases

- A user follows `.claude` guidance and sees `/code`, while `.codex` guidance says `/apply`
- `docs/team-ai-workflow.md` still describes `/code` as the implementation step after the rename is implemented
- `/design` or `/tdd` still suggests running `/code`, which breaks the end-to-end handoff story

## Expected Outcomes

- All active user-facing workflow references converge on `/apply`
- Mirrored skill files stay aligned instead of drifting
- Human review and handoff instructions remain coherent from `/propose` through `/apply`

## Notes for Implementation

- Search both active and mirrored skill trees before calling the docs done
- Do not spend time rewriting archived artifacts unless they are actually surfaced to users in the active workflow
