# Test Scenario: 3 verification

- Source task: 3.1 Generate test scenarios with `/tdd issue-1-improve-create-pr-branch-flow`.
- Source task: 3.2 Run OpenSpec validation for `issue-1-improve-create-pr-branch-flow`.
- Source task: 3.3 Search `/github:create-pr` guidance to verify it no longer blocks only because current branch is `main`.
- Related requirements:
  - `github-orchestration-skills`: Create PR starts on main.
  - `github-orchestration-skills`: Create PR starts on existing work branch.
  - `github-orchestration-skills`: Create PR publishes local work.
  - `github-orchestration-skills`: Create PR cannot publish safely.
- Assumptions:
  - `/tdd` uses scenario mode because no runnable test framework is detected.
  - `/apply` or human verification will run OpenSpec validation after implementation.

## Happy Path

- Khi `/tdd issue-1-improve-create-pr-branch-flow` hoàn tất
- Thì `tests/` có scenario coverage cho main/default branch start, existing work branch, safe publish, blocked states, missing issue, missing verification, và residual risk.
- Khi implementation xong
- Thì `openspec validate issue-1-improve-create-pr-branch-flow --strict` pass.
- Khi search `.codex/skills/github-create-pr/SKILL.md`
- Thì không còn guidance block chỉ vì current branch là `main`; thay vào đó guidance tạo/switch sang `codex/` branch khi safe.

## Edge Cases

- Search vẫn thấy chữ `main` trong context blocker, nhưng wording phải phân biệt unsafe branch với supported main-start flow.
- Validation pass nhưng scenarios thiếu unsafe dirty state; coverage review phải bắt thiếu sót này.
- Existing non-main branch guidance tồn tại nhưng không nêu unsafe publish blocker; coverage review phải bắt thiếu sót này.

## Error Cases

- Không có scenario test cho starting on `main`.
- Không có scenario test cho preserving existing work branch.
- Không có scenario test cho missing issue linkage, missing verification, hoặc missing residual risk.
- `/github:create-pr` guidance vẫn nói block khi current branch là `main` mà không có create-branch path.
- TDD output claim đã chạy validation, trái guardrail.

## Expected Outcomes

- Scenario tests đủ để `/apply` dùng làm behavioral context.
- Verification task có tiêu chí cụ thể, không chỉ checklist chung chung.
- Change không được coi complete nếu `/github:create-pr` vẫn block mechanical main-start flow.

## Notes for Implementation

- `/tdd` không chạy validation.
- Sau approval, bước tiếp theo là `/apply issue-1-improve-create-pr-branch-flow`.
