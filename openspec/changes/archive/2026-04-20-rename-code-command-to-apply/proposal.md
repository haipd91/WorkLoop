## Why

The `/code` command currently represents applying approved change artifacts to a workflow, but its name suggests code generation rather than change application. Renaming the command to `/apply` aligns the user-facing workflow with the real domain behavior and removes a recurring source of confusion.

This change is needed now because the current term leaks the wrong mental model into commands, docs, and artifact handoff expectations. Keeping `/code` as the public term makes the workflow harder to understand and harder to extend consistently.

## What Changes

- **BREAKING** Rename the user-facing implementation command from `/code` to `/apply`
- Remove `/code` as a supported user-facing command instead of keeping a compatibility alias
- Update user-facing workflow documentation, prompts, and handoff language to reference `/apply`
- Update workflow expectations so implementation handoff artifacts point to `/apply` as the execution step
- Revise related specs and generated artifacts so they describe `/apply` as the implementation entrypoint

## Capabilities

### New Capabilities
- None

### Modified Capabilities
- `code-skill`: rename the implementation entrypoint from `/code` to `/apply` and preserve its artifact-loading behavior under the new command
- `design-skill`: update design handoff requirements so approved `design.md` is preserved for `/apply` instead of `/code`
- `tdd-skill`: update test handoff requirements so generated tests are reviewed and handed off to `/apply` instead of `/code`

## Impact

- User-facing command surface for implementation workflow
- Skill instructions and prompts that currently direct users to `/code`
- OpenSpec capability documents referencing `/code` as the implementation handoff command
- Any command routing, workflow dispatch, and UI/help text that expose the implementation command name
