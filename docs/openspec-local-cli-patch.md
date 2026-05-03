# OpenSpec Local CLI Patch

## Why

This repo currently needs one local OpenSpec CLI patch for the review graph flow.

OpenSpec CLI `1.3.0` supports schema-driven artifacts, so adding `review` to
`openspec/schemas/spec-driven-custom/schema.yaml` is enough for:

- `openspec status --change <name>`
- `openspec instructions review --change <name>`

But `openspec instructions apply --change <name>` has hardcoded all-done text
that says a completed implementation is ready to archive. This repo needs that
handoff to route to `/review <name>` first.

## Local Files Patched

These files are outside this repository and will not move with the repo:

- `/opt/homebrew/lib/node_modules/@fission-ai/openspec/dist/core/artifact-graph/types.js`
- `/opt/homebrew/lib/node_modules/@fission-ai/openspec/dist/core/artifact-graph/types.d.ts`
- `/opt/homebrew/lib/node_modules/@fission-ai/openspec/dist/commands/workflow/instructions.js`

The patch adds schema support for:

```yaml
apply:
  doneInstruction: |
    All implementation tasks are complete. Run `/review <name>` before archive.
```

`<name>` is replaced with the active change name by the patched CLI.

## Migration Checklist

When moving to a new machine or reinstalling OpenSpec:

1. Install OpenSpec.
2. Run `openspec --version` and confirm the behavior.
3. If all-done apply instructions still mention archive directly, reapply the local CLI patch.
4. Verify with an all-tasks-complete change:

```bash
openspec instructions apply --change "<change-name>" --json
```

Expected:

- `state` is `all_done`
- `instruction` routes to `/review <change-name>`
- `instruction` does not say the change is ready to archive

## Preferred Long-Term Fix

Do not keep this as a silent machine-local change forever.

Preferred options:

1. Upstream `apply.doneInstruction` support into OpenSpec.
2. Vendor or pin a patched OpenSpec package for this repo.
3. Add a repo script that checks and reapplies the patch explicitly.

Until one of those exists, treat this document as required setup knowledge for
machines that run the custom OpenSpec workflow.
