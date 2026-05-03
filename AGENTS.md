# Codex Rules

Behavioral guidelines to reduce common Codex coding mistakes. Merge with project-specific instructions as needed.

Tradeoff: These guidelines bias toward small diffs, correctness, and verification over speed. For trivial tasks, use judgment.

## 1. Think Before Editing

Do not assume silently. Surface uncertainty early.

Before implementing:

- State assumptions that affect the solution.
- If the request has multiple meanings, ask or present options.
- If the requested approach is risky or overcomplicated, push back.
- If something is unclear enough to cause wrong work, stop and ask.

## 2. Simplicity First

Minimum code that solves the problem. Nothing speculative.

- No features beyond what was asked.
- No abstractions for single-use code.
- No flexibility or configurability that was not requested.
- No new dependency unless clearly justified.
- If the change feels larger than the problem, simplify it.

Ask yourself: "Would a senior engineer call this overcomplicated?" If yes, rewrite.

## 3. Surgical Changes

Touch only what you must. Protect user work.

When editing existing code:

- Match existing style and patterns.
- Do not reformat unrelated code.
- Do not refactor adjacent code unless required.
- Do not remove unrelated dead code; mention it instead.
- Never revert user changes unless explicitly asked.

When your changes create unused code:

- Remove imports, variables, functions, or files made unused by your change.
- Do not clean up pre-existing issues unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

Define success criteria. Verify before claiming done.

Transform tasks into verifiable goals:

- "Fix the bug" -> reproduce it, fix it, verify it no longer happens.
- "Add validation" -> test invalid inputs, implement validation, verify behavior.
- "Refactor X" -> preserve behavior, run relevant checks.
- "Improve UI" -> define visible result, implement it, verify in browser/screenshot when practical.

For multi-step tasks, state a brief plan:

1. Inspect relevant files -> verify: identify exact change surface.
2. Implement the smallest fix -> verify: review diff.
3. Run focused checks -> verify: tests, typecheck, lint, or manual validation.

If verification cannot be run, say why and state the remaining risk.

These guidelines are working if: diffs are smaller, fewer unrelated changes appear, clarifying questions happen before mistakes, and verification becomes routine.
