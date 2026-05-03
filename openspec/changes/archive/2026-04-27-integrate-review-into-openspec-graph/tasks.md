## 1. Schema Graph Integration

- [x] 1.1 Add `review` artifact to `openspec/schemas/spec-driven-custom/schema.yaml` with output path `review.md`.
- [x] 1.2 Set `review` dependencies to proposal, specs, design, tasks, and tests.
- [x] 1.3 Keep `apply.requires` limited to specs, tasks, and tests so review is not required before implementation.
- [x] 1.4 Add schema instruction text for `review` that includes context files and review artifact generation guidance.
- [x] 1.5 Ensure `review: done` means `review.md` exists, not that review verdict passed.

## 2. Apply Instruction Integration

- [x] 2.1 Update schema-level apply all-done instruction so completed implementation routes to `/review <name>`.
- [x] 2.2 Verify `openspec instructions apply --change <name> --json` no longer tells users the change is ready to archive.

## 3. Review Instruction Integration

- [x] 3.1 Verify `openspec status --change <name>` includes `review`.
- [x] 3.2 Verify `review` is blocked while prior artifacts are missing.
- [x] 3.3 Verify `review` is ready when prior artifacts are complete and `review.md` is absent.
- [x] 3.4 Verify `review` is done when `review.md` exists.
- [x] 3.5 Verify `openspec instructions review --change <name> --json` returns output path, dependencies, and review context.
- [x] 3.6 If schema-only dispatch fails, add a minimal generic artifact instruction bridge.

## 4. Archive Bridge

- [x] 4.1 Update `/archive` guidance to read OpenSpec review artifact state when available.
- [x] 4.2 Preserve phase 1 metadata parsing for verdict, blocking findings, and stale review state.
- [x] 4.3 Document fallback behavior if OpenSpec CLI cannot expose review graph state directly.
- [x] 4.4 Verify archive does not treat `review: done` as verdict pass.

## 5. Docs And Regression Checks

- [x] 5.1 Update workflow docs to describe graph-aware review.
- [x] 5.2 Update specs impacted by review graph integration.
- [x] 5.3 Verify existing phase 1 behavior still passes: `/review` writes `review.md` and `/archive` blocks failing review state.
