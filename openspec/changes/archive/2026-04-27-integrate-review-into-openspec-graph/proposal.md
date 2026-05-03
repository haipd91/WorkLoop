## Why

Phase 1 made `/review` a durable skill-level quality gate, but OpenSpec still does not model `review.md` as a first-class artifact. This leaves `openspec status` and `openspec instructions apply` with incomplete workflow knowledge, including generic archive-oriented all-done guidance.

## What Changes

- Add `review` as a first-class artifact in the `spec-driven-custom` schema graph.
- Make `openspec status --change <name>` show review status and dependency state.
- Make `openspec instructions review --change <name> --json` return context files, dependencies, output path, and review-specific guidance.
- Update apply all-done guidance so OpenSpec routes completed implementation to `/review`, not archive.
- Keep existing `/review` stance and archive fail-closed behavior from phase 1.
- Document any CLI limitation with a minimal bridge instead of weakening the review gate.

## Capabilities

### New Capabilities

- `openspec-review-artifact`: Defines review as a first-class OpenSpec artifact with status, instructions, dependencies, and output path.

### Modified Capabilities

- `review-quality-gate`: Review gate behavior changes from skill-level-only enforcement to OpenSpec graph-aware enforcement.
- `code-skill`: Apply all-done behavior changes so OpenSpec-generated apply instructions route to `/review` instead of archive.

## Impact

- Affected schema/config: `openspec/schemas/spec-driven-custom/schema.yaml`.
- Affected workflow skills/docs: `/review`, `/archive`, `/apply`, and AI Native workflow docs.
- Affected OpenSpec behavior: `status`, `instructions review`, and apply all-done instruction text.
- No new dependency.
- No application runtime behavior change.
