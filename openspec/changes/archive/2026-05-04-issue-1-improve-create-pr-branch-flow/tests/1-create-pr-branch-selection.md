# Test Scenario: 1 create-pr-branch-selection

- Source task: 1.1 Update active `github-orchestration-skills` spec for `/github:create-pr` branch creation and publish behavior.
- Source task: 1.2 Verify the delta spec includes main/default-branch, existing work branch, publish, and blocked-state scenarios.
- Source task: 2.1 Update `.codex/skills/github-create-pr/SKILL.md` to create and switch to `codex/<issue-or-change-slug>` when starting on `main` or the default branch.
- Source task: 2.2 Preserve existing non-main branch behavior when the human already selected a work branch.
- Related requirements:
  - `github-orchestration-skills`: Create PR starts on main.
  - `github-orchestration-skills`: Create PR starts on existing work branch.
- Assumptions:
  - Repository default base branch is discoverable from local git or GitHub metadata.
  - Issue link and OpenSpec change name can produce a stable branch slug.
  - Branch prefix remains `codex/`.

## Happy Path

- Khi `/github:create-pr issue-1-improve-create-pr-branch-flow` chạy từ `main` hoặc default base branch
- Và local work thuộc đúng OpenSpec change, có issue link, verification, và residual risk
- Thì skill tạo và switch sang branch `codex/issue-1-improve-create-pr-branch-flow` trước khi stage, commit, push, hoặc tạo PR.
- Khi `/github:create-pr` chạy từ non-main work branch do human đã chọn
- Thì skill giữ branch đó làm PR head branch, miễn branch an toàn để publish.

## Edge Cases

- Current branch tên khác `main` nhưng trùng default base branch của repo.
- Branch `codex/issue-1-improve-create-pr-branch-flow` đã tồn tại và trỏ đúng intended work; skill dùng lại thay vì tạo branch mới.
- Human cung cấp branch hint khác `codex/` nhưng branch đó đã là work branch hợp lệ; skill không tự đổi branch nếu không cần.
- Issue number có sẵn nhưng change slug dài; branch name vẫn readable và không chứa ký tự không hợp lệ.

## Error Cases

- Branch đích đã tồn tại nhưng trỏ tới work khác.
- Current branch là non-main nhưng có upstream/base không rõ hoặc có dấu hiệu unsafe to publish.
- Không xác định được issue number hoặc change slug để tạo branch an toàn.
- Có unrelated dirty changes khiến scope branch không rõ.

## Expected Outcomes

- `/github:create-pr` không block chỉ vì đang ở `main`.
- Skill chỉ tạo/switch branch trước publish khi branch context an toàn.
- Human-selected non-main branch được giữ nguyên khi hợp lệ.
- Unsafe branch collision hoặc unclear branch context block với handoff rõ.

## Notes for Implementation

- Branch creation phải xảy ra trước staging/commit.
- Không publish từ `main` hoặc default base branch.
- Không force-update branch đã tồn tại nếu chưa xác minh nó thuộc intended work.
