---
name: archive
description: Archive a completed change in the experimental workflow. Use when the user wants to finalize and archive a change after implementation is complete.
license: MIT
compatibility: Requires openspec CLI.
metadata:
  author: openspec
  version: "1.0"
  generatedBy: "1.3.0"
---

Archive a completed change in the experimental workflow.

**Input**: Optionally specify a change name. If omitted, check if it can be inferred from conversation context. If vague or ambiguous you MUST prompt for available changes.

**Steps**

1. **If no change name provided, prompt for selection**

   Run `openspec list --json` to get available changes. Use the **AskUserQuestion tool** to let the user select.

   Show only active changes (not already archived).
   Include the schema used for each change if available.

   **IMPORTANT**: Do NOT guess or auto-select a change. Always let the user choose.

2. **Check artifact completion status**

   Run `openspec status --change "<name>" --json` to check artifact completion.

   Parse the JSON to understand:
   - `schemaName`: The workflow being used
   - `artifacts`: List of artifacts with their status (`done` or other)

   **If any artifacts are not `done`:**
   - Display warning listing incomplete artifacts
   - Use **AskUserQuestion tool** to confirm user wants to proceed
   - Proceed if user confirms

3. **Check task completion status**

   Read the tasks file (typically `tasks.md`) to check for incomplete tasks.

   Count tasks marked with `- [ ]` (incomplete) vs `- [x]` (complete).

   **If incomplete tasks found:**
   - Display warning showing count of incomplete tasks
   - Use **AskUserQuestion tool** to confirm user wants to proceed
   - Proceed if user confirms

   **If no tasks file exists:** Proceed without task-related warning.

4. **Check review quality gate**

   Review gate failures are hard blockers, not warnings.

   First, inspect the OpenSpec artifact graph from the status JSON collected in
   step 2.

   **If the schema exposes a `review` artifact:**
   - If `review.status` is not `done`, stop and tell the user to run
     `/review <name>` before archive.
   - If `review.status` is `done`, continue to parse `review.md` metadata.
   - Do not treat `review.status: done` as a passing verdict. It only means
     `review.md` exists.

   **If the schema does not expose a `review` artifact:**
   - Use the direct `review.md` metadata check below as the compatibility
     bridge for older schemas or CLI versions.

   Check for `openspec/changes/<name>/review.md`.

   **If `review.md` is missing:**
   - Stop.
   - Tell the user to run `/review <name>` before archive.

   Parse the machine-checkable metadata block at the top of `review.md`:

   ```yaml
   verdict: pass
   blocking_findings: none
   reviewed_ref: <git HEAD>
   reviewed_diff: <sha256 fingerprint>
   reviewed_implementation_diff: <sha256 implementation fingerprint>
   reviewed_paths_excluded: openspec/changes/<name>/review.md
   reviewed_at: <ISO-8601 timestamp>
   ```

   Required metadata:
   - `verdict`
   - `blocking_findings`
   - `reviewed_ref`
   - `reviewed_diff`
   - `reviewed_implementation_diff`
   - `reviewed_paths_excluded`
   - `reviewed_at`

   Accepted values:
   - `verdict`: only `pass` allows archive
   - `blocking_findings`: only `none` allows archive
   - `reviewed_paths_excluded`: exactly `openspec/changes/<name>/review.md`

   **Block archive when:**
   - required metadata is missing
   - metadata is duplicated, contradictory, malformed, or unparseable
   - `verdict` is not `pass`
   - `blocking_findings` is not `none`

   Check stale review state:

   ```bash
   git rev-parse HEAD
   (git diff --binary && git diff --cached --binary) | shasum -a 256
   (git diff --binary -- . ':(exclude)openspec/changes/<name>/review.md' && git diff --cached --binary -- . ':(exclude)openspec/changes/<name>/review.md') | shasum -a 256
   ```

   Use `reviewed_ref` and `reviewed_diff` for traceability and legacy diagnostics. The stale gate MUST compare the current implementation fingerprint against `reviewed_implementation_diff`, excluding only the path in `reviewed_paths_excluded`.

   If the implementation fingerprint matches, continue even when `HEAD` differs from `reviewed_ref` because the only post-review change is `openspec/changes/<name>/review.md`.

   If the implementation fingerprint differs, block archive and tell the user to rerun `/review <name>`.

   Before proceeding, show:
   - review verdict
   - blocking findings state
   - residual risk summary from `review.md`

5. **Check GitHub delivery evidence**

   Archive must fail closed for GitHub-backed changes when durable GitHub
   delivery evidence is missing.

   Determine whether the change is GitHub-backed or explicit local-only by
   checking, in order:
   - human-provided issue or PR target in the request or conversation
   - issue or PR links in the change artifacts, `review.md`, or task notes
   - current branch or git remote context that clearly identifies a PR-backed
     branch
   - explicit local-only acceptance documented in the artifacts or `review.md`

   **For GitHub-backed changes, require durable PR or merge evidence:**
   - PR URL or number
   - issue linkage when known
   - merge record or explicit handoff from `/github:merge <pr>`

   **Block archive when:**
   - the change is GitHub-backed and no PR or merge evidence is available
   - `/review` passed but no PR, merge record, or accepted local-only bypass
     exists
   - GitHub-backed versus local-only status is ambiguous and no explicit
     local-only acceptance is documented

   Blocked message:

   ```text
   Archive blocked: GitHub delivery evidence is missing. Run `/github:merge <pr>` or document an explicit local-only exception with residual risk.
   ```

   **For explicit local-only or legacy bypass:**
   - require documented human acceptance
   - require residual risk in `review.md`
   - show the bypass reason before final archive action

   Before proceeding, show:
   - PR link or merge record for GitHub-backed changes
   - issue linkage when known
   - local-only or legacy bypass reason when applicable

6. **Assess delta spec sync state**

   Check for delta specs at `openspec/changes/<name>/specs/`. If none exist, proceed without sync prompt.

   **If delta specs exist:**
   - Compare each delta spec with its corresponding main spec at `openspec/specs/<capability>/spec.md`
   - Determine what changes would be applied (adds, modifications, removals, renames)
   - Show a combined summary before prompting

   **Prompt options:**
   - If changes needed: "Sync now (recommended)", "Archive without syncing"
   - If already synced: "Archive now", "Sync anyway", "Cancel"

   If user chooses sync, use Task tool (subagent_type: "general-purpose", prompt: "Use Skill tool to invoke openspec-sync-specs for change '<name>'. Delta spec analysis: <include the analyzed delta spec summary>"). Proceed to archive regardless of choice.

7. **Perform the archive**

   Create the archive directory if it doesn't exist:

   ```bash
   mkdir -p openspec/changes/archive
   ```

   Generate target name using current date: `YYYY-MM-DD-<change-name>`

   **Check if target already exists:**
   - If yes: Fail with error, suggest renaming existing archive or using different date
   - If no: Move the change directory to archive

   ```bash
   mv openspec/changes/<name> openspec/changes/archive/YYYY-MM-DD-<name>
   ```

8. **Display summary**

   Show archive completion summary including:
   - Change name
   - Schema that was used
   - Archive location
   - GitHub delivery evidence or documented local-only bypass
   - Whether specs were synced (if applicable)
   - Note about any warnings (incomplete artifacts/tasks)

**Output On Success**

```
## Archive Complete

**Change:** <change-name>
**Schema:** <schema-name>
**Archived to:** openspec/changes/archive/YYYY-MM-DD-<name>/
**Specs:** ✓ Synced to main specs (or "No delta specs" or "Sync skipped")
**GitHub:** PR/merge evidence verified (or "Local-only bypass documented")

All artifacts complete. All tasks complete.
```

**Guardrails**

- Always prompt for change selection if not provided
- Use artifact graph (openspec status --json) for completion checking
- Don't block archive on artifact/task warnings - just inform and confirm
- Do block archive on review gate failures: missing, malformed, stale, non-pass, or blocking `review.md`
- Do block archive for GitHub-backed changes when PR or merge evidence is missing
- Do require documented human acceptance and residual risk before treating a change as local-only
- Preserve .openspec.yaml when moving to archive (it moves with the directory)
- Show clear summary of what happened
- If sync is requested, use openspec-sync-specs approach (agent-driven)
- If delta specs exist, always run the sync assessment and show the combined summary before prompting
