---
name: review
description: Review an applied OpenSpec change before archive, producing a durable review.md quality gate artifact.
license: MIT
compatibility: Requires git and OpenSpec artifacts.
metadata:
  author: agentica
  version: "1.0"
---

Review an applied OpenSpec change before archive.

`/review` is a quality gate between `/apply` and `/archive`. It reviews implementation against approved artifacts and writes `openspec/changes/<name>/review.md`.

## Input

Optionally specify a change name. If omitted:

- Infer from conversation context if clear.
- Otherwise run `openspec list --json` and ask the user to select.

Always announce: `Using change: <name>`.

## Steps

### 1. Read Change Context

If available, run:

```bash
openspec instructions review --change "<name>" --json
```

Use the returned `contextFiles`, dependencies, and `outputPath` as the
schema-backed review artifact contract. If the command is unavailable for an
older schema, fall back to the file list below.

Read these files when present:

- `openspec/changes/<name>/proposal.md`
- `openspec/changes/<name>/specs/**/*.md`
- `openspec/changes/<name>/design.md`
- `openspec/changes/<name>/tasks.md`
- `openspec/changes/<name>/tests/**/*`

If an optional artifact is missing, continue but record it under Residual Risk.

### 2. Inspect Implementation State

Collect review state:

```bash
git rev-parse HEAD
git diff --stat
git diff
git diff --cached
```

Use the diff to compare implementation against proposal, specs, design, tasks, and tests.

Create a deterministic review fingerprint:

```bash
(git diff --binary && git diff --cached --binary) | shasum -a 256
```

The fingerprint command must still run for an empty diff; empty review state is still a concrete SHA-256 value.

### 3. Review Stance

Use code-review stance:

- prioritize bugs, behavioral regressions, scope drift, missing tests, unsafe workflow gaps, and archive bypass risks
- check implementation against change artifacts, not against chat memory
- do not treat docs-only changes as automatically safe
- distinguish blocking findings from non-blocking findings

Blocking findings include:

- implementation violates proposal/spec/design/tasks
- required workflow gate can be bypassed
- archive can proceed with missing, malformed, failing, or stale review evidence
- active docs still direct users from `/apply` straight to `/archive`
- verification required by tests is not performed and risk is not documented

### 4. Write `review.md`

Write `openspec/changes/<name>/review.md`.

The file MUST start with this machine-checkable metadata block:

```yaml
verdict: pass
blocking_findings: none
reviewed_ref: <git HEAD>
reviewed_diff: <sha256 fingerprint>
reviewed_at: <ISO-8601 timestamp>
```

Allowed values:

- `verdict`: `pass` or `fail`
- `blocking_findings`: `none` or `present`

Required sections:

```md
## Findings

## Tests Run

## Residual Risk

## Verdict
```

If blocking findings exist:

- set `verdict: fail`
- set `blocking_findings: present`
- list blocking findings first under `## Findings`

If no findings exist, say that explicitly under `## Findings`.

If no tests/checks were run, record that under `## Tests Run` and explain under `## Residual Risk`.

### Legacy Bypass

For a pre-gate legacy change, a maintainer may document bypass in `review.md`. Bypass still requires:

- `verdict: pass`
- `blocking_findings: none`
- a Residual Risk entry explaining why bypass is accepted

Do not use implicit bypass.

## Output

After writing `review.md`, summarize:

- verdict
- blocking findings state
- tests/checks run
- residual risk
- reviewed ref/diff state

If verdict is pass, say:

```text
Review passed. Run `/archive <name>` when ready.
```

If verdict is fail, say:

```text
Review failed. Fix blocking findings, then rerun `/review <name>`.
```

## Guardrails

- Do not archive.
- Do not implement fixes during `/review`.
- Do not treat OpenSpec `review: done` as verdict pass; archive must still parse metadata.
- Do not write pass verdict when blocking findings remain.
- Do not hide missing artifacts or skipped tests.
- Do not rely on chat-only review evidence.
- Keep `review.md` deterministic enough for `/archive` to parse.
