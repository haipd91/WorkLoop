# Test Scenario: 1 command surface cutover

- Source tasks:
  - 1.1 Locate the active user-facing command registration for the implementation step and replace `/code` with `/apply`
  - 1.2 Remove `/code` from the supported command surface instead of forwarding it to `/apply`
  - 1.3 Preserve the existing implementation artifact-loading behavior under `/apply`, including optional `tests/` context
- Related requirements:
  - Read tests directory as implementation context
- Assumptions:
  - The repo or host environment has one authoritative registration path for the public implementation command
  - `/apply` should reuse the same implementation flow that `/code` used previously

## Happy Path

- When the implementation command is registered for users
- Then `/apply` is the only exposed public command for the implementation step
- And `/apply` loads change artifacts the same way `/code` used to load them
- And `/apply` reads `openspec/changes/<name>/tests/` when that directory exists

## Edge Cases

- The command registration is split across more than one surface, such as local skill files plus host-level command metadata
- Internal identifiers or filenames still contain `code`, but they are not user-facing and do not violate the public contract
- Some changes do not have a `tests/` directory, and `/apply` must still proceed using proposal, design, and tasks artifacts only

## Error Cases

- `/code` is still invokable after the rename because one command surface was missed
- `/apply` exists but no longer reads `tests/`, causing a regression in implementation context
- `/apply` is wired to a different flow than `/code`, creating unintended behavior changes during the rename

## Expected Outcomes

- Users can invoke `/apply` for implementation without losing any existing artifact-loading behavior
- `/code` is no longer a supported user-facing command
- The rename changes public semantics, not the underlying implementation workflow

## Notes for Implementation

- Verify the actual command registration path before editing docs, otherwise the repo can claim `/apply` while the runtime still exposes `/code`
- Treat silent `/code` forwarding as a failure for this change, not as acceptable compatibility
