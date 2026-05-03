# Test Scenario: 2 apply instruction integration

- Source tasks:
  - 2.1 Update schema-level apply all-done instruction
  - 2.2 Verify apply instructions no longer say ready to archive
- Related requirements:
  - Hand off to /review after implementation
- Assumptions:
  - `openspec instructions apply --change <name> --json` is the source for `/apply` dynamic guidance.

## Happy Path

- Khi all implementation tasks are complete
- And `openspec instructions apply --change <name> --json` runs
- Thì instruction text routes user to `/review <name>`
- And instruction text does not say the change is ready to archive.

## Edge Cases

- Khi tasks are incomplete
- Thì apply guidance continues to tell user to work through pending tasks.

- Khi `review.md` already exists
- Thì apply all-done guidance still must not skip archive gate semantics.

## Error Cases

- Khi apply all-done guidance says `ready to archive`
- Thì regression exists because implementation bypasses review in CLI guidance.

- Khi `.codex/skills/apply/SKILL.md` says `/review` but schema apply instruction says archive
- Thì skill/schema drift exists.

## Expected Outcomes

- CLI-generated apply guidance and `/apply` skill agree.
- Completed implementation routes to `/review`, not archive.

## Notes for Implementation

- Verify the actual JSON instruction text, not just docs.
