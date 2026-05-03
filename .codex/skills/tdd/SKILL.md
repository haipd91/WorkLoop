---
name: tdd
description: Generate test files from change artifacts before implementation. Use when the user wants to define test cases from proposal, specs, design, and tasks — creating test files as input for /apply.
license: MIT
compatibility: Claude Code with AskUserQuestion tool support.
metadata:
  author: agentica
  version: "1.0"
---

Generate test files for a change from its artifacts, saving to `openspec/changes/<name>/tests/`.

**Input**: Optionally specify a change name (e.g., `/tdd add-auth`). If omitted, infer from context or prompt.

---

## Steps

### 1. Select the change

If a name is provided, use it. Otherwise:
- Infer from conversation context
- If ambiguous, run `openspec list --json` and use **AskUserQuestion tool** to let user select

### 2. Read all available artifacts

Read each of the following if it exists:
- `openspec/changes/<name>/proposal.md`
- `openspec/changes/<name>/specs/**/*.md`
- `openspec/changes/<name>/design.md`
- `openspec/changes/<name>/tasks.md`

Use all available content — do not fail if some files are missing.

### 3. Detect test framework and choose output mode

Scan project files in this order:
1. `CLAUDE.md` — look for test framework mention
2. `package.json` — check `devDependencies` for jest, vitest, mocha, etc.
3. `pyproject.toml` or `setup.cfg` — check for pytest
4. `go.mod` — Go standard testing
5. Any `*.test.*` or `*_test.*` files in the project

Decide mode:

- **If one framework is detected clearly:** use **runnable mode** and generate test code in that framework's conventions
- **If multiple frameworks look plausible and confidence is low:** use **AskUserQuestion tool**:
  > "I found multiple possible test frameworks. Which one should `/tdd` use?"
- **If no framework is detected:** use **scenario mode** and generate structured Markdown test specs instead of blocking

### 4. Generate test files

For each task in `tasks.md`:
- Map task + related specs/acceptance criteria → test cases
- Each test file covers: **happy path**, **edge cases**, **error cases**
- Save to `openspec/changes/<name>/tests/`

**Runnable mode file naming:**
- `<task-id>.test.ts`
- `test_<task-id>.py`
- `<task-id>_test.go`

Include a comment block at the top of each runnable test file:
```ts
// Source: task <id> — <task description>
// Assumptions: <any assumptions made about behavior>
```

**Scenario mode file naming:**
- `<task-id>-<slug>.md`
- or `<capability>-<slug>.md` when capability grouping is clearer

Each scenario file MUST include:
- source task or capability
- related requirements
- assumptions
- happy path
- edge cases
- error cases
- expected outcomes
- notes for implementation

Example:
```md
# Test Scenario: 2.2 parallel-analysis

- Source task: 2.2 Implement chạy parallel agents, mỗi agent phụ trách một nhóm criteria độc lập
- Related requirements:
  - Analyze impact through grouped parallel criteria
- Assumptions:
  - Hệ thống có thể spawn nhiều agent chạy đồng thời

## Happy Path
- Khi `/design` chạy với đủ artifacts
- Thì mỗi criteria group được giao cho đúng một agent và kết quả được trả về để tổng hợp

## Edge Cases
- Một criteria group không có đủ context nhưng các group khác vẫn chạy

## Error Cases
- Một agent thất bại giữa chừng

## Expected Outcomes
- Design draft vẫn nêu rõ group nào hoàn tất, group nào cần follow-up

## Notes for Implementation
- Không để hai agent cùng phân tích một criteria giống nhau
```

**Important:** Generate tests that define intent and behavior — not tests tied to implementation internals.

### 5. Human review loop

Display a summary:
```
Generated X test files in openspec/changes/<name>/tests/:
  - <filename>: N test cases (happy path, M edge cases, K error cases)
  - ...
```

If scenario mode was used, say so explicitly in the summary.

Use **AskUserQuestion tool**:
> "Test files are ready for review. Would you like to approve or request changes?"

Options: `["Approve — save and proceed", "Request changes"]`

- If **Approve**: confirm save, suggest next step
- If **Request changes**: ask what to change (open-ended), update relevant files, show summary again, ask again

### 6. Confirm and suggest next step

After approval:
1. Confirm: "Saved to `openspec/changes/<name>/tests/`"
2. Suggest: "Run `/apply <name>` to implement — it will use these test files as context."

---

## Guardrails

- Prefer runnable mode when framework detection is clear
- Use scenario mode by default when no framework can be detected
- Ask human only when framework detection is ambiguous or the user explicitly wants runnable tests
- Never save without human approval via AskUserQuestion
- Tests should describe behavior, not implementation details
- If a task is too vague to generate meaningful tests, note it explicitly and skip rather than invent
- Maximum 1 clarification round per ambiguity — don't interrogate
- Do not run or verify the generated tests
